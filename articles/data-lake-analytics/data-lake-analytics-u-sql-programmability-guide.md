---
title: Podręcznik programowania U-SQL dla usługi Azure Data Lake | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zbiór usług w usłudze Azure Data Lake, które umożliwiają tworzenie platformy danych big data oparte na chmurze.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: saveenr
ms.openlocfilehash: 400057b5ce79cdcf6c7651462e9f497bf647e930
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="u-sql-programmability-guide"></a>Podręcznik programowania U-SQL

U-SQL jest język zapytania, które jest przeznaczone do typów danych dużych obciążeń. Jedną z funkcji unikatowy U-SQL jest kombinacją języka deklaratywne przypominającego SQL z rozszerzeń i programowania, który znajduje się w języku C#. W tym przewodniku możemy skupić się na rozszerzania i programowania języka U-SQL, który został włączony w języku C#.

## <a name="requirements"></a>Wymagania

Pobierz i zainstaluj [Azure Data Lake Tools dla programu Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Wprowadzenie do języka U-SQL  

Przyjrzyj się poniższy skrypt U-SQL:

```
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Ten skrypt definiuje dwa zestawy wierszy: `@a` i `@results`. Zestaw wierszy `@results` jest zdefiniowana z `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Typy C# i wyrażenia w skryptu U-SQL

Wyrażenie U-SQL jest wyrażenie C# połączone z operacji logicznych U-SQL takich `AND`, `OR`, i `NOT`. Wyrażenia języka U-SQL mogą być używane z SELECT, EXTRACT, gdzie wystąpienia, Grupuj według, ZADEKLAROWAĆ. Na przykład poniższy skrypt analizuje ciąg jako wartość daty i godziny.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Poniższy fragment analizuje ciąg jako wartość daty i godziny w instrukcji DECLARE.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Używanie wyrażeń C# dla konwersje typów danych

W poniższym przykładzie pokazano, jak data/godzina konwersji danych za pomocą wyrażeń C#. W tym scenariuszu określonego ciągu danych daty i godziny jest konwertowana na format daty i godziny o północy 00:00:00 czasu notacji.

```
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Używanie wyrażeń C# dla bieżącej daty

Możliwość ściągania dzisiaj, możemy użyć następującego wyrażenia języka C#: `DateTime.Now.ToString("M/d/yyyy")`

Poniżej przedstawiono przykład sposobu użycia tego wyrażenia w skrypcie:

```
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Za pomocą zestawów platformy .NET

U-SQL modelu rozszerzalności odgrywa możliwość dodawania niestandardowych kodów z zestawów platformy .NET. 

### <a name="register-a-net-assembly"></a>Rejestrowanie zestawów platformy .NET

Użyj `CREATE ASSEMBLY` instrukcji, aby umieścić zestaw .NET do języka U-SQL bazy danych. Później, skryptów U-SQL można użyć tych zestawów przy użyciu `REFERENCE ASSEMBLY` instrukcji. 

Poniższy kod przedstawia sposób rejestrowania zestawu:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Poniższy kod przedstawia sposób odwołanie do zestawu:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Zapoznaj się [instrukcje rejestracji zestawu](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) który obejmuje w tym temacie bardziej szczegółowo.


### <a name="use-assembly-versioning"></a>Użyj wersji zestawu
Obecnie U-SQL używa .NET Framework w wersji 4.5. Dlatego upewnij się, że własne zestawy są zgodne z tą wersją środowiska uruchomieniowego.

Jak wspomniano wcześniej, kod działa U-SQL w formacie 64-bitowej (x 64). Tak upewnij się, że kod jest skompilowana do uruchamiania na x64. W przeciwnym razie błąd niepoprawny format przedstawiona wcześniej.

Każdy przekazany zestaw biblioteki DLL i pliku zasobów, takich jak innego środowiska uruchomieniowego, natywny zestaw lub pliku konfiguracji, może mieć maksymalnie 400 MB. Całkowity rozmiar wdrożonych zasobów za pomocą wdrażania zasobów lub za pomocą odwołania do zestawów i ich dodatkowe pliki, nie może przekraczać 3 GB.

Na koniec należy pamiętać, że każda baza danych U-SQL może zawierać tylko jedną wersję żadnych danego zestawu. Na przykład jeśli potrzebujesz zarówno w wersji 7, jak i w wersji 8 biblioteki NewtonSoft Json.Net, musisz zarejestrować je w dwóch różnych baz danych. Ponadto każdy skrypt może odwoływać się tylko do jednej wersji danego zestawu biblioteki DLL. W związku z tym U-SQL następuje C# zestawu zarządzania i wersji semantykę.

## <a name="use-user-defined-functions-udf"></a>Użyj funkcji zdefiniowanej przez użytkownika: funkcji zdefiniowanej przez użytkownika
Funkcje zdefiniowane przez użytkownika U-SQL lub funkcji zdefiniowanej przez użytkownika, są programowania procedur, które akceptują parametrów, wykonywania akcji (na przykład złożonych obliczeń) i zwraca wynik tego działania jako wartość. Wartość zwracana funkcji zdefiniowanej przez użytkownika może być tylko jeden skalarnej. UDF języka U-SQL może zostać wywołany w podstawowej skryptu U-SQL, takich jak wszystkie inne C# skalarną.

Firma Microsoft zaleca, aby zainicjować U-SQL funkcje zdefiniowane przez użytkownika jako **publicznego** i **statycznych**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Pierwszy Przyjrzyjmy się prosty przykład tworzenia funkcji zdefiniowanej przez użytkownika.

W tym scenariuszu przypadek użycia należy ustalić okres, w tym kwartał i miesiąc obrachunkowy przy pierwszym logowaniu dla określonego użytkownika. Pierwszy miesiąc obrachunkowy roku w naszym scenariuszu jest czerwca.

Aby obliczyć okres, wprowadzeniu następujących funkcji języka C#:

```
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Po prostu oblicza miesiąc obrachunkowy i kwartał i zwraca wartość typu ciąg. Czerwca pierwszy miesiąc pierwszy kwartał obrachunkowy, możemy użyć "Q1:P1". Lipca możemy użyć "Q1:P2" i tak dalej.

Jest to zwykły C# funkcję, która firma Microsoft ma być używane w naszych projektu U-SQL.

Oto, jak wygląda sekcji kodu powiązanego w tym scenariuszu:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Teraz zamierzamy wywołanie tej funkcji z podstawowej skryptu U-SQL. Aby to zrobić, musimy udostępnić w pełni kwalifikowaną nazwę funkcji, łącznie z przestrzenią nazw, która jest w tym przypadku NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Rzeczywiste podstawowy skrypt U-SQL jest następujący:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Poniżej znajduje się plik wyjściowy wykonywania skryptów:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

W tym przykładzie przedstawiono prosty użycie wbudowanej funkcji zdefiniowanej przez użytkownika w języku U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Zachowaj stanu między wywołań funkcji zdefiniowanej przez użytkownika
Obiekty programowania U-SQL C# można można bardziej zaawansowane opcje, wykorzystując interakcyjności za pomocą zmiennych globalnych związane z kodem. Przyjrzyjmy się następujące scenariusza biznesowego w przypadku użycia.

W dużych organizacjach użytkownicy mogą przełączać odmian wewnętrznych aplikacji. Obejmują one programu Microsoft Dynamics CRM, usługa Power Bi i tak dalej. Klienci mogą mają dotyczyć analizy danych telemetrycznych sposób użytkownicy będą przełączać się między różnymi aplikacjami, jakie są trendy użycia, i tak dalej. Jest celem w firmie w celu zoptymalizowania użycia aplikacji. Może być połączyć różnych aplikacji lub określonych procedur logowania jednokrotnego.

Na osiągnięcie tego celu, musimy określić identyfikatory sesji i zwłoki czas od ostatniej sesji, który wystąpił.

Musimy Znajdź poprzednie logowanie, a następnie przypisz to logowanie do wszystkich sesji, które są generowane na tej samej aplikacji. Pierwszego wyzwania jest, że podstawowy skrypt U-SQL nie umożliwiają nałożenia obliczeń na już obliczane kolumny z funkcja LAG. Drugie żądanie jest mamy zachować określonej sesji dla wszystkich sesji, w tym samym okresie czasu.

Aby rozwiązać ten problem, używamy wewnątrz sekcji kodem — zmienna globalna: `static public string globalSession;`.

Tę zmienną globalną jest stosowana do całego zestawu wierszy podczas wykonywania naszych skryptu.

Oto sekcji CodeBehind nasz program U-SQL:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

W tym przykładzie pokazano zmiennej globalnej `static public string globalSession;` używany wewnątrz `getStampUserSession` funkcji i uzyskiwanie za każdym parametrze sesji zostanie zmieniona.

Podstawowy skrypt U-SQL jest następujący:

```
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

Funkcja `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` jest tutaj wywoływana podczas obliczania drugiego zestawu wierszy pamięci. Przekazuje ono `UserSessionTimestamp` kolumny i zwraca wartość do `UserSessionTimestamp` została zmieniona.

Plik wyjściowy jest następujący:

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

W tym przykładzie przedstawiono bardziej skomplikowane scenariusz przypadek użycia, w którym firma Microsoft przy użyciu zmiennej globalnej wewnątrz sekcji związane z kodem, który jest stosowany do wierszy całej pamięci.

## <a name="use-user-defined-types-udt"></a>Używanie typów zdefiniowanych przez użytkownika: UDT
Typy definiowane przez użytkownika lub UDT, jest inna funkcja programowalności U-SQL. UDT U-SQL zachowuje się jak zwykły C# zdefiniowane przez użytkownika typu. C# to silnie typizowaną język, który zezwala na korzystanie z wbudowane i niestandardowe typy danych zdefiniowane przez użytkownika.

U-SQL nie można niejawnie serializacji lub zdeserializować dowolnego UDTs podczas przekazywania UDT między wierzchołków w zestawów wierszy. Oznacza to, że użytkownik musi podać jawne elementu formatującego za pomocą interfejsu IFormatter. To zapewnia serializacja U-SQL i zdeserializować metody dla typu.

> [!NOTE]
> U-SQL ekstraktory wbudowanych i outputters obecnie nie można serializować lub zdeserializować UDT danych do i z plików, nawet w przypadku zestawu IFormatter. Dlatego podczas zapisywania danych UDT pliku z instrukcją dane wyjściowe, lub odczytywania go z katalogu, należy przekazać ją w postaci ciągu lub tablicy typu byte. Następnie wywołaj serializacji i deserializacji jawnie kodu (to znaczy metodę ToString() UDT). Zdefiniowane przez użytkownika ekstraktory i outputters, z drugiej strony, można odczytują i zapisują typów.

Jeśli firma Microsoft spróbuj użyć UDT w katalogu lub OUTPUTTER (Brak poprzedniej wybierz), jak pokazano poniżej:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Firma Microsoft komunikat o błędzie:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Aby pracować z UDT w outputter, albo mamy do serializacji ciągu z metodę ToString() lub tworzenie niestandardowych outputter.

Typów nie można obecnie używać w GROUP BY. Jeśli UDT jest używany w GROUP BY, zostanie zgłoszony następujący błąd:

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Aby zdefiniować UDT, musimy:

* Dodaj następujących przestrzeni nazw:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Dodaj `Microsoft.Analytics.Interfaces`, co jest wymagane dla interfejsów UDT. Ponadto `System.IO` mogą być wymagane do zdefiniowania interfejsu IFormatter.

* Zdefiniuj typ zdefiniowane z atrybutem SqlUserDefinedType.

**SqlUserDefinedType** służy do oznaczania definicja typu w zestawie jako typ zdefiniowany przez użytkownika (UDT) w języku U-SQL. Właściwości w ustawieniach atrybutu odzwierciedlają charakterystyki fizycznej UDT. Ta klasa nie może być dziedziczona.

SqlUserDefinedType jest wymagany atrybut UDT definicji.

Konstruktor klasy:  

* SqlUserDefinedTypeAttribute (elementu formatującego typu)

* Program formatujący typ: wymaganego parametru do definiowania elementu formatującego UDT — w szczególności typ `IFormatter` interfejsu muszą być przekazywane w tym miejscu.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typowy UDT wymaga również definicji interfejsu IFormatter, jak pokazano w poniższym przykładzie:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter` Interfejsu serializuje i zwalnia serializuje wykres obiektu z typem głównego \<typeparamref name = "T" >.

\<typeparam name = "T" > typu głównego do serializacji i deserializacji wykresu obiektu.

* **Deserializacja**: cofnąć serializuje dane dla podanego strumienia i reconstitutes wykresu obiektów.

* **Serializować**: Serializuje obiekt lub grafu obiektów z danym elementem głównym do dostarczonego strumienia.

`MyType` wystąpienie: wystąpienie typu.  
`IColumnWriter` Moduł zapisujący / `IColumnReader` czytnika: zasadniczy strumień kolumny.  
`ISerializationContext` kontekst: wyliczenia, który definiuje zestaw flagi Określa kontekst źródła lub miejsca docelowego dla tego strumienia podczas serializacji.

* **Pośredni**: Określa, czy kontekst źródłowy lub docelowy nie jest utrwalonego magazynu.

* **Trwałość**: Określa, że źródłowy lub docelowy kontekst magazynu trwałego.

Jako regularne C# typ, definicji UDT U-SQL mogą zawierać zastąpień dla operatorów takich jak +/ == /! =. Może również obejmować metod statycznych. Na przykład, jeśli firma Microsoft będzie używany ten UDT jako parametr do funkcji agregujących MIN U-SQL, musimy zdefiniować < zastąpienie operatora.

Wcześniej w tym przewodniku, możemy przedstawiono przykład obrachunkowym identyfikacji okresu od określonej daty w formacie `Qn:Pn (Q1:P10)`. Poniższy przykład przedstawia sposób definiowania typu niestandardowego dla wartości okresu obrachunkowym.

Poniżej przedstawiono przykład sekcji kodu powiązanego z niestandardowy interfejs UDT i IFormatter:

```
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Zdefiniowanego typu zawiera dwie liczb: kwartał i miesiąc. Operatory `==/!=/>/<` i metody statycznej `ToString()` są zdefiniowane w tym miejscu.

Jak wspomniano wcześniej, można używać w wyrażeniach wybierz UDT, ale nie można używać w OUTPUTTER/EKSTRAKTOR bez niestandardowej serializacji. Albo musi być Zserializowany jako ciągu z `ToString()` używane z niestandardowych OUTPUTTER/WYODRĘBNIANIA.

Teraz omówimy użycia UDT. W sekcji związane z kodem możemy zmienione naszych funkcja GetFiscalPeriod do następującego:

```
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Jak widać, zwraca wartość typu naszych FiscalPeriod.

W tym miejscu udostępniamy przykładem użyć go dalej w podstawowej skryptu U-SQL. W tym przykładzie przedstawiono różne formy UDT wywołanie skryptu U-SQL.

```
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Oto przykład pełnego kodem sekcji:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Użyj agregacje zdefiniowane przez użytkownika: UDAGG
Agregacje zdefiniowane przez użytkownika są wszystkie funkcje związane z agregacji, które są niewysłanych out-of--box języku U-SQL. Przykład może być agregacją, aby wykonać obliczenia matematyczne niestandardowych, konkatenacji ciągów, manipulacje z ciągów itd.

Zdefiniowane przez użytkownika agregacji klasy podstawowej definicji jest następujący:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** wskazuje, że typ powinien zostać zarejestrowany jako agregacji zdefiniowanej przez użytkownika. Ta klasa nie może być dziedziczona.

Atrybut SqlUserDefinedType jest **opcjonalne** UDAGG definicji.


Klasa podstawowa umożliwia przekazywanie trzy parametry abstrakcyjne: dwa jako parametry wejściowe i jeden w wyniku. Typy danych są zmienne i powinien być zdefiniowany podczas dziedziczenia klas.

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** wywołuje jeden raz dla każdej grupy podczas obliczania. Procedura inicjowania zapewnia dla każdej grupy agregacji.  
* **Accumulate** jest wykonywane raz dla każdej wartości. Udostępnia ona funkcje głównego dla algorytmu agregacji. Może służyć do wartości zagregowanych z różnych typów danych, które są zdefiniowane podczas dziedziczenia klas. Może akceptować dwa parametry o typach danych zmiennej.
* **Przerwanie** jest wykonywane raz dla każdej grupy agregacji po zakończeniu przetwarzania zwracać wynik dla każdej grupy.

Aby zadeklarować poprawne dane wejściowe i typów danych wyjściowych, użyj definicji klasy w następujący sposób:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Pierwszy parametr accumulate
* T2: Pierwszy parametr accumulate
* TResult: Zwracany typ przerwania

Na przykład:

```
public class GuidAggregate : IAggregate<string, int, int>
```

lub

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Użyj UDAGG w języku U-SQL
Aby użyć UDAGG, zdefiniuj je w CodeBehind lub odwołania z istniejących programowania DLL zgodnie z wcześniejszym opisem.

Następnie należy użyć następującej składni:

```
AGG<UDAGG_functionname>(param1,param2)
```

Oto przykład UDAGG:

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

I podstawowego skryptu U-SQL:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

W tym scenariuszu przypadek użycia możemy łączenie identyfikatorów GUID klas dla określonych użytkowników.

## <a name="use-user-defined-objects-udo"></a>Za pomocą obiektów zdefiniowanych przez użytkownika: UDO
U-SQL umożliwia zdefiniowanie programowania niestandardowych obiektów, które są nazywane obiekty zdefiniowane przez użytkownika lub UDO.

Poniżej przedstawiono listę UDO w języku U-SQL:

* Ekstraktory zdefiniowane przez użytkownika
    * Wyodrębnij wiersz po wierszu
    * Używane do implementowania wyodrębniania danych z niestandardowej strukturze plików

* Outputters zdefiniowane przez użytkownika
    * Dane wyjściowe wiersz po wierszu
    * Używane do danych wyjściowych niestandardowe typy danych lub niestandardowego pliku formatów

* Procesory zdefiniowane przez użytkownika
    * Jeden wiersz i tworzące jeden wiersz
    * Zmniejsz liczbę kolumn lub utworzyć nowe kolumny z wartościami, które pochodzą z istniejącego zestawu kolumn

* Appliers zdefiniowane przez użytkownika
    * Jeden wiersz i tworzące 0 do n wierszy
    * Używane z Zastosuj zewnętrzne/między

* Combiners zdefiniowane przez użytkownika
    * Zestawy wierszy — sprzężenia zdefiniowane przez użytkownika łączy

* Reduktory zdefiniowane przez użytkownika
    * N wierszy i tworzące jeden wiersz
    * Używany do ograniczenia liczby wierszy

UDO jest zwykle nazywany jawnie skryptu U-SQL w ramach następujących instrukcji U-SQL:

* WYODRĘBNIJ
* DANE WYJŚCIOWE
* PROCES
* ŁĄCZENIE
* ZMNIEJSZ

> [!NOTE]  
> Dla obiektu UDO są ograniczone użycie 0,5 Gb pamięci RAM.  To ograniczenie pamięci nie ma zastosowania do wykonania lokalnego.

## <a name="use-user-defined-extractors"></a>Użyj ekstraktory zdefiniowane przez użytkownika
U-SQL służy do importowania danych zewnętrznych przy użyciu instrukcji WYODRĘBNIANIA. Instrukcja WYODRĘBNIANIA można użyć wbudowanych ekstraktory UDO:  

* *Extractors.Text()*: zapewnia wyodrębniania z plików tekstowych rozdzielanych różnych kodowań.

* *Extractors.Csv()*: zapewnia wyodrębniania z wartościami rozdzielanymi przecinkami (CSV) plików różnych kodowań.

* *Extractors.Tsv()*: zapewnia wyodrębniania z wartości tabulatorami (TSV) plików różnych kodowań.

Może być przydatne do opracowywania ekstraktor niestandardowych. Może to być przydatne podczas importowania danych, jeśli chcemy wykonaj jedną z następujących czynności:

* Modyfikowanie danych wejściowych podział kolumn i modyfikując poszczególnych wartości. Funkcjonalność PROCESORA jest lepszym rozwiązaniem dla łączenie kolumn.
* Przeanalizować danych niestrukturalnych, takich jak strony sieci Web i wiadomości e-mail lub częściowo bez struktury danych, takich jak XML/JSON.
* Analizować dane przy użyciu kodowania nieobsługiwany.

Aby zdefiniować wyodrębniania zdefiniowanych przez użytkownika lub LUCZ, należy utworzyć `IExtractor` interfejsu. Wszystkie parametry do katalogu, takie jak ogranicznik wiersza/kolumny wejściowe i kodowanie, muszą być zdefiniowane w konstruktorze klasy. `IExtractor` Interfejsu powinien również zawierać definicji `IEnumerable<IRow>` zastąpienia w następujący sposób:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**SqlUserDefinedExtractor** atrybut wskazuje, że typ powinien zostać zarejestrowany jako ekstraktor zdefiniowane przez użytkownika. Ta klasa nie może być dziedziczona.

SqlUserDefinedExtractor jest opcjonalny atrybut LUCZ definicji. Go używać do definiowania właściwości AtomicFileProcessing dla obiekt LUCZ.

* wartość logiczna AtomicFileProcessing   

* **wartość true,** = wskazuje, że ta ekstraktor wymaga atomic plików wejściowych (JSON, XML,...)
* **FALSE** = wskazuje, że ten ekstraktor można postępowania w przypadku plików podział / rozproszone (CSV, SEQ,...)

Obiekty główne programowania LUCZ **wejściowych** i **dane wyjściowe**. Obiekt wejściowy jest używany do wyliczenia danych wejściowych jako `IUnstructuredReader`. Obiekt danych wyjściowych jest używany do ustawienia danych wyjściowych w wyniku działania ekstraktor.

Dane wejściowe jest dostępny za pośrednictwem `System.IO.Stream` i `System.IO.StreamReader`.

Kolumny wejściowe wyliczenia firma Microsoft najpierw podziału strumienia wejściowego przy użyciu ogranicznik wiersza.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Następnie dodatkowo podzielić wejściowych wiersza części kolumny.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Aby ustawić dane wyjściowe, używamy `output.Set` metody.

Należy zrozumieć, że niestandardowe ekstraktor tylko generuje kolumny i wartości, które są zdefiniowane z danych wyjściowych. Ustaw wywołania metody.

```
output.Set<string>(count, part);
```

Rzeczywiste wyodrębnianie danych wyjściowych jest wyzwalane przez wywołanie metody `yield return output.AsReadOnly();`.

Poniżej przedstawiono przykład ekstraktor:

```
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

W tym scenariuszu przypadek użycia ekstraktor generuje identyfikator GUID dla kolumny "guid" i konwertuje wartości kolumny "użytkownika" na wielkie litery. Niestandardowe ekstraktory może wygenerować bardziej skomplikowane wyniki analizy danych wejściowych i manipulowanie go.

Poniżej przedstawiono podstawowe skryptu U-SQL, który używa ekstraktor niestandardowych:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Użyj outputters zdefiniowane przez użytkownika
Zdefiniowane przez użytkownika outputter jest innego obiektu UDO U-SQL, który umożliwia rozszerzanie wbudowanej funkcji U-SQL. Podobnie jak wyodrębnianie, istnieje kilka wbudowanych outputters.

* *Outputters.Text()*: zapisuje dane w plikach tekstowych rozdzielanych różnych kodowań.
* *Outputters.Csv()*: zapisuje dane do plików różnych kodowań plik wartości rozdzielanych przecinkami (CSV).
* *Outputters.Tsv()*: zapisuje dane do plików różnych kodowań wartość tabulatorami (TSV).

Niestandardowe outputter pozwala na zapis danych w niestandardowym formacie zdefiniowane. Może to być przydatne w przypadku następujących zadań:

* Zapisywanie danych w plikach częściową strukturą lub bez struktury.
* Zapisywanie danych nie obsługuje kodowania.
* Modyfikowanie danych wyjściowych lub dodanie atrybutów niestandardowych.

Aby zdefiniować outputter zdefiniowane przez użytkownika, należy utworzyć `IOutputter` interfejsu.

Poniżej znajduje się podstawowym `IOutputter` Implementacja klasy:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Wszystkie parametry wejściowe outputter, takich jak ogranicznik wiersza/kolumny, kodowanie i tak dalej, muszą być zdefiniowane w konstruktorze klasy. `IOutputter` Interfejsu powinien również zawierać definicji `void Output` zastąpienia. Atrybut `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` Opcjonalnie można ustawić dla przetwarzania plików atomic. Aby uzyskać więcej informacji zobacz następujące informacje.

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` jest wywoływana dla każdego wiersza wejściowego. Zwraca `IUnstructuredWriter output` zestawu wierszy.
* Konstruktor klasy jest używany do przekazania parametrów do outputter zdefiniowane przez użytkownika.
* `Close` Służy do zastępowania opcjonalnie kosztowne stanu wersji lub określić, kiedy ostatni wiersz został zapisany.

**SqlUserDefinedOutputter** atrybut wskazuje, że typ powinien zostać zarejestrowany jako outputter zdefiniowane przez użytkownika. Ta klasa nie może być dziedziczona.

SqlUserDefinedOutputter jest opcjonalny atrybut dla definicji outputter zdefiniowane przez użytkownika. Służy do definiowania właściwości AtomicFileProcessing.

* wartość logiczna AtomicFileProcessing   

* **wartość true,** = wskazuje, że ten outputter wymaga pliki wyjściowe niepodzielne (JSON, XML,...)
* **FALSE** = wskazuje, że ten outputter można postępowania w przypadku plików podział / rozproszone (CSV, SEQ,...)

Obiekty główne programowania są **wiersza** i **dane wyjściowe**. **Wiersza** obiekt jest używany do wyliczenia dane wyjściowe jako `IRow` interfejsu. **Dane wyjściowe** służy do ustawiania dane wyjściowe do pliku docelowego.

Danych wyjściowych jest dostępny za pośrednictwem `IRow` interfejsu. Dane wyjściowe są przekazywane wiersza w czasie.

Poszczególne wartości wyliczane są przez wywołanie metody Get interfejsu IRow:

```
row.Get<string>("column_name")
```

Można ustalić nazwy poszczególnych kolumn przez wywołanie metody `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Takie podejście umożliwia tworzenie elastycznych outputter dla żadnego schematu metadanych.

Dane są zapisywane do pliku przy użyciu `System.IO.StreamWriter`. Parametr strumienia jest ustawiony na `output.BaseStrea` jako część `IUnstructuredWriter output`.

Należy pamiętać, że ważne jest, aby opróżnić buforu danych do pliku po każdej iteracji wiersza. Ponadto `StreamWriter` z atrybutu jednorazowe włączone (ustawienie domyślne) i można użyć obiektu **przy użyciu** — słowo kluczowe:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

W przeciwnym razie wywołanie metody Flush() jawnie po każdej iteracji. Ten zostanie przedstawiony w następującym przykładzie.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ustaw nagłówki i stopki dla outputter zdefiniowane przez użytkownika
Aby ustawić nagłówka, należy użyć przepływu wykonywania iteracji jednego.

```
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Kod w pierwszym `if (isHeaderRow)` bloku jest wykonywane tylko raz.

Stopki, użyj odwołania do wystąpienia `System.IO.Stream` obiektu (`output.BaseStream`). Zapisywanie stopki w metody Close() `IOutputter` interfejsu.  (Aby uzyskać więcej informacji, zobacz poniższy przykład).

Poniżej przedstawiono przykład outputter zdefiniowane przez użytkownika:

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

I podstawowy skrypt U-SQL:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Jest to outputter HTML, który tworzy plik HTML z danych tabeli.

### <a name="call-outputter-from-u-sql-base-script"></a>Wywołanie outputter z podstawowej skryptu U-SQL
Aby wywołać outputter niestandardowych z podstawowej skryptu U-SQL, nowe wystąpienie obiektu outputter musi być utworzony.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Aby uniknąć tworzenia wystąpienia obiektu w skrypcie podstawowej, można utworzyć otoki funkcji, jak pokazano w naszym przykładzie wcześniej:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

W takim przypadku wywołania oryginalnego wygląda następująco:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Używają procesorów zdefiniowane przez użytkownika
Procesor zdefiniowane przez użytkownika lub UDP, jest typem obiektu UDO U-SQL, który umożliwia przetwarzanie przychodzących wierszy, stosując funkcje programowania. UDP umożliwia łączenie kolumn, zmodyfikuj wartości, a w razie potrzeby należy dodać nowe kolumny. Zasadniczo pomaga przetworzyć zestawu wierszy do tworzenia wymaganych elementów danych.

Aby zdefiniować UDP, należy utworzyć `IProcessor` interfejsu z `SqlUserDefinedProcessor` atrybut, który jest opcjonalne dla protokołu UDP.

Ten interfejs musi zawierać definicję dla `IRow` zastąpić interfejsu zestawu wierszy, jak pokazano w poniższym przykładzie:

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** wskazuje, że typ powinien być zarejestrowany jako procesor zdefiniowane przez użytkownika. Ta klasa nie może być dziedziczona.

Atrybut SqlUserDefinedProcessor jest **opcjonalne** dla definicji UDP.

Obiekty główne programowania są **wejściowych** i **dane wyjściowe**. Obiekt wejściowy służy do wyliczenia kolumny wejściowe i wyjściowe oraz ustawić dane wyjściowe w wyniku aktywności procesora.

Wyliczenia kolumny wejściowe używamy `input.Get` metody.

```
string column_name = input.Get<string>("column_name");
```

Parametr `input.Get` metody jest przekazywany jako część kolumny `PRODUCE` klauzuli `PROCESS` instrukcji podstawowy skrypt U-SQL. Należy użyć prawidłowy typ danych w tym miejscu.

Dla danych wyjściowych, użyj `output.Set` metody.

Należy pamiętać, że niestandardowe producentów wyświetla tylko kolumny i wartości, które są zdefiniowane z `output.Set` wywołania metody.

```
output.Set<string>("mycolumn", mycolumn);
```

Procesor rzeczywiste dane wyjściowe zostanie wywołany przez wywołanie metody `return output.AsReadOnly();`.

Poniżej przedstawiono przykład procesora:

```
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

W tym scenariuszu przypadek użycia procesora generuje nową kolumnę o nazwie "full_description" łącząc istniejące kolumny — w tym przypadku "użytkownika" wielkimi literami i "des". Również generuje identyfikator GUID i zwraca oryginalnego i nowej wartości identyfikatora GUID.

Jak widać w poprzednim przykładzie, należy wywołać metodę C# podczas `output.Set` wywołania metody.

Poniżej przedstawiono przykład podstawowego skryptu U-SQL, który używa niestandardowego procesora:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Użyj appliers zdefiniowane przez użytkownika
Zdefiniowane przez użytkownika applier U-SQL umożliwia wywołanie niestandardowej funkcji języka C# dla każdego wiersza, który jest zwracany przez wyrażenie Tabela zewnętrzna zapytania. Odpowiednie dane wejściowe jest obliczane dla każdego wiersza wejściowego po lewej stronie, a wiersze, które są tworzone są łączone na ostateczne dane wyjściowe. Lista kolumn, które są tworzone przez operatora APPLY są kombinacją zestawu kolumn w lewej i prawej danych wejściowych.

Zdefiniowane przez użytkownika applier jest wywoływany jako część wyrażenia USQL wybierz.

Typowy wywołanie applier użytkownika wygląda następująco:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Aby uzyskać więcej informacji o używaniu appliers w wyrażeniu SELECT, zobacz [U-SQL wybierz wybrać między Zastosuj i zewnętrzne](https://msdn.microsoft.com/library/azure/mt621307.aspx).

Zdefiniowane przez użytkownika definicji klasy podstawowej applier wygląda następująco:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Aby zdefiniować applier zdefiniowane przez użytkownika, należy utworzyć `IApplier` interfejsu z [`SqlUserDefinedApplier`] atrybut, który jest opcjonalne dla definicji applier zdefiniowane przez użytkownika.

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Zastosuj jest wywoływana dla każdego wiersza tabeli zewnętrznej. Zwraca `IUpdatableRow` output zestawu wierszy.
* Konstruktor klasy jest używany do przekazania parametrów do applier zdefiniowane przez użytkownika.

**SqlUserDefinedApplier** wskazuje, że typ powinien zostać zarejestrowany jako applier zdefiniowane przez użytkownika. Ta klasa nie może być dziedziczona.

**SqlUserDefinedApplier** jest **opcjonalne** dla definicji applier zdefiniowane przez użytkownika.


Obiekty główne programowania znajdują się w następujący sposób:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Wejściowe zestawy wierszy są przekazywane jako `IRow` wejściowego. Wiersze dane wyjściowe są generowane jako `IUpdatableRow` interfejsu danych wyjściowych.

Można ustalić nazwy poszczególnych kolumn przez wywołanie metody `IRow` metody schematu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Aby uzyskać rzeczywiste dane z przychodzącego `IRow`, możemy użyć metody Get() `IRow` interfejsu.

```
mycolumn = row.Get<int>("mycolumn")
```

Lub używamy nazwy kolumn schematu:

```
row.Get<int>(row.Schema[0].Name)
```

Należy ustawić wartości danych wyjściowych z `IUpdatableRow` danych wyjściowych:

```
output.Set<int>("mycolumn", mycolumn)
```

Ważne jest, aby zrozumieć, że niestandardowe appliers output tylko kolumny i wartości, które są zdefiniowane z `output.Set` wywołania metody.

Rzeczywiste dane wyjściowe zostanie wywołany przez wywołanie metody `yield return output.AsReadOnly();`.

Parametry applier zdefiniowane przez użytkownika mogą być przekazywane do konstruktora. Applier może zwracać zmienną liczbę kolumn, które muszą zostać zdefiniowane podczas wywołania applier w podstawowej skryptu U-SQL.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Oto przykład applier zdefiniowane przez użytkownika:

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Poniżej przedstawiono podstawowe skryptu U-SQL dla tego użytkownika applier:

```
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

W tym scenariuszu przypadków użycia applier zdefiniowane przez użytkownika działa jako wartości rozdzielane przecinkami analizatora składni właściwości floty samochodów. Wiersze pliku wejściowego wyglądać następująco:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Shevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Jest to typowe tabulacji TSV plik z kolumną właściwości zawierającego właściwości samochodu, takich jak marka i model. Te właściwości należy przeanalizować do kolumn tabeli. Applier, który został dostarczony umożliwia także wygenerować dynamiczne liczba właściwości w zestawie wierszy wyników, na podstawie parametru, który jest przekazywany. Można wygenerować właściwości wszystkich lub określonych tylko właściwości.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Zdefiniowane przez użytkownika applier można wywołać jako nowe wystąpienie obiektu applier:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Lub wywołanie metody fabryki otoki:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Użyj combiners zdefiniowane przez użytkownika
Zdefiniowane przez użytkownika łączenia lub UDC, umożliwia połączenie wiersze z lewego i prawego zestawów wierszy, opartych na logice niestandardowej. Zdefiniowane przez użytkownika łączenia jest używany z łączenia wyrażenia.

Łączenia jest wywoływany z wyrażeniem łączenia, który dostarcza niezbędne informacje o zarówno wejściowe zestawy wierszy, kolumny grupowania, oczekiwany wynik schematu i dodatkowe informacje.

Aby wywołać łączenia w podstawowej skryptu U-SQL, możemy użyć następującej składni:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Aby uzyskać więcej informacji, zobacz [łączenia wyrażenia (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx).

Aby zdefiniować łączenia zdefiniowane przez użytkownika, należy utworzyć `ICombiner` interfejsu z [`SqlUserDefinedCombiner`] atrybut, który jest opcjonalne dla definicji łączenia zdefiniowane przez użytkownika.

Podstawa `ICombiner` definicji klasy:

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Implementacji niestandardowego `ICombiner` interfejsu powinny zawierać definicji `IEnumerable<IRow>` połączyć zastąpienia.

```
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

**SqlUserDefinedCombiner** atrybut wskazuje, że typ powinien zostać zarejestrowany jako łączenia zdefiniowane przez użytkownika. Ta klasa nie może być dziedziczona.

**SqlUserDefinedCombiner** służy do definiowania właściwości trybie łączenia. Jest opcjonalny atrybut dla definicji łączenia zdefiniowane przez użytkownika.

Tryb CombinerMode

Wyliczenia CombinerMode może przyjmować następujące wartości:

* Pełny (0), każdy wiersz danych wyjściowych zależy od potencjalnie wszystkie wiersze danych wejściowych od lewej i prawej z taką samą wartość klucza.

* Od lewej (1) każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz wejściowych z lewej strony (i potencjalnie wszystkie wiersze z prawej strony, z taką samą wartość klucza).

* Prawo każdego wiersza danych wyjściowych jest zależna od pojedynczy wiersz wejściowych z prawej (i potencjalnie wszystkie wiersze z lewej strony o tej samej wartości klucza) (2).

* Wewnętrzna (3) w pojedynczym wierszu wejściowego w lewo i prawo z taką samą wartość zależy od każdego wiersza danych wyjściowych.

Przykład: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Obiekty główne programowania są:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Wejściowe zestawy wierszy są przekazywane jako **po lewej stronie** i **prawo** `IRowset` typ interfejsu. Oba zestawy wierszy musi zostać wyliczone do przetwarzania. Można tylko wyliczyć każdy interfejs, a więc musimy wyliczyć i jego pamięci podręcznej, jeśli to konieczne.

Na potrzeby buforowania, można utworzyć listy\<T\> typ konstrukcji pamięci w związku z tym składnika LINQ wykonywanie zapytania, w szczególności listę <`IRow`>. Typ anonimowy danych może służyć podczas wyliczania również.

Zobacz [wprowadzenie do kwerend LINQ (C#)](https://msdn.microsoft.com/library/bb397906.aspx) uzyskać więcej informacji dotyczących zapytań LINQ i [IEnumerable\<T\> interfejsu](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) Aby uzyskać więcej informacji na temat interfejsu IEnumerable\<T\> interfejsu.

Aby uzyskać rzeczywiste dane z przychodzącego `IRowset`, możemy użyć metody Get() `IRow` interfejsu.

```
mycolumn = row.Get<int>("mycolumn")
```

Można ustalić nazwy poszczególnych kolumn przez wywołanie metody `IRow` metody schematu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Lub przy użyciu nazwy kolumn schematu:

```
c# row.Get<int>(row.Schema[0].Name)
```

Ogólne wyliczenia za pomocą LINQ wygląda następująco:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Po wyliczania oba zestawy wierszy, zamierzamy pętli wszystkich wierszy. Dla każdego wiersza w zestawie wierszy po lewej stronie zamierzamy Znajdź wszystkie wiersze, które spełniają warunek naszych łączenia.

Należy ustawić wartości danych wyjściowych z `IUpdatableRow` danych wyjściowych.

```
output.Set<int>("mycolumn", mycolumn)
```

Rzeczywiste wyniki jest wyzwalany przez wywołanie do `yield return output.AsReadOnly();`.

Poniżej przedstawiono przykład łączenia:

```
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

W tym scenariuszu przypadek użycia możemy są budowania raportu analizy sprzedaży detalicznej. Celem jest, aby znaleźć wszystkie produkty, które koszt ponad 20 000 $ i który sprzedaży za pośrednictwem witryny sieci Web szybciej niż w sprzedaży detalicznej regularnie w danym okresie.

Poniżej przedstawiono podstawowe skryptu U-SQL. Możesz porównać logiki między sprzężenia regularnego i łączenia:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Zdefiniowane przez użytkownika łączenia można wywołać jako nowe wystąpienie obiektu applier:

```
USING new MyNameSpace.MyCombiner();
```


Lub wywołanie metody fabryki otoki:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Użyj reduktory zdefiniowane przez użytkownika

U-SQL umożliwia pisanie reduktory niestandardowego zestawu wierszy w języku C# z wykorzystaniem strukturę rozszerzalności zdefiniowane przez użytkownika operatora i implementowanie interfejsu IReducer.

Reduktor zdefiniowane przez użytkownika lub przez, można usunąć niepotrzebne wierszy podczas wyodrębniania danych (Importuj). On również można manipulować i ocena wierszy i kolumn. Opartych na logice programowania, jego można również zdefiniować wiersze, które muszą zostać wyodrębniony.

Aby zdefiniować przez klasę, należy utworzyć `IReducer` interfejsu z opcjonalną `SqlUserDefinedReducer` atrybutu.

Ten interfejs klasy powinny zawierać definicji `IEnumerable` zastąpienie interfejsu zestawu wierszy.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**SqlUserDefinedReducer** atrybut wskazuje, że typ powinien być zarejestrowany jako reduktor zdefiniowane przez użytkownika. Ta klasa nie może być dziedziczona.
**SqlUserDefinedReducer** jest opcjonalny atrybut dla definicji reduktor zdefiniowane przez użytkownika. Służy do definiowania właściwości IsRecursive.

* wartość logiczna IsRecursive    
* **wartość true,** = wskazuje, czy jest to reduktor asocjacyjnych i przemienne

Obiekty główne programowania są **wejściowych** i **dane wyjściowe**. Obiekt wejściowy jest używany do wyliczenia wiersze danych wejściowych. Dane wyjściowe służy do ustawiania wiersze danych wyjściowych wyniku zmniejszenie działania.

Wyliczenia wiersze danych wejściowych używamy `Row.Get` metody.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametr `Row.Get` metody jest przekazywany jako część kolumny `PRODUCE` klasy `REDUCE` instrukcji podstawowy skrypt U-SQL. Należy również za pomocą prawidłowy typ danych w tym miejscu.

Dla danych wyjściowych, użyj `output.Set` metody.

Ważne jest, aby zrozumieć, w tym niestandardowych reduktor tylko wartości, które są zdefiniowane z danych wyjściowych `output.Set` wywołania metody.

```
output.Set<string>("mycolumn", guid);
```

Dane wyjściowe rzeczywiste reduktor zostanie wywołany przez wywołanie metody `yield return output.AsReadOnly();`.

Poniżej przedstawiono przykład reduktor:

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

W tym scenariuszu przypadek użycia reduktor pomija wiersze z Pusta nazwa użytkownika. Dla każdego wiersza w zestawie wierszy go odczytuje każdej wymaganej kolumny, a następnie oblicza długość nazwy użytkownika. Rzeczywiste wiersza go generuje tylko wtedy, gdy długość wartości nazwy użytkownika jest większa niż 0.

Poniżej przedstawiono podstawowy skryptu U-SQL, który używa niestandardowych reduktor:

```
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
