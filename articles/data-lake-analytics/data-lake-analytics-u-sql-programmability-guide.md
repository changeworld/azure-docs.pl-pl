---
title: Podręcznik programowania U-SQL dla usługi Azure Data Lake
description: Dowiedz się więcej informacji na temat zestawu usług Azure Data Lake Analytics, które umożliwiają tworzenie platformę danych big Data w chmurze.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: d1b230b40d1f880787334ebfd39e704e3a650baa
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489660"
---
# <a name="u-sql-programmability-guide"></a>Podręcznik programowania U-SQL

U-SQL to język zapytań, który jest przeznaczony dla typu danych dużych obciążeń. Jedna z unikatowych funkcji języka U-SQL to kombinacja języka deklaratywnego podobnego do SQL za pomocą rozszerzeń i programowania, który znajduje się w języku C#. W tym przewodniku możemy skoncentrować się na rozszerzania i programowania języka U-SQL, który jest włączony w języku C#.

## <a name="requirements"></a>Wymagania

Pobierz i zainstaluj [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

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

## <a name="c-types-and-expressions-in-u-sql-script"></a>Typy języka C# i wyrażenia w skrypcie U-SQL

Wyrażenie języka U-SQL jest wyrażenie języka C# w połączeniu z języka U-SQL operacji logicznych takie `AND`, `OR`, i `NOT`. Możliwość używania wyrażeń języka U-SQL przy użyciu SELECT, EXTRACT, WHERE, problemy, Grupuj według i ZADEKLARUJ. Na przykład poniższy skrypt analizuje ciąg jako wartość daty/godziny.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Poniższy fragment kodu analizuje ciąg jako wartość daty/godziny w instrukcji DECLARE.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Należy używać wyrażeń języka C# dla konwersje typów danych

W poniższym przykładzie pokazano, jak konwersja danych daty i godziny przy użyciu wyrażeń języka C#. W tym scenariuszu dane daty/godziny w ciągu jest konwertowana na format daty i godziny przy użyciu notacji godziny 00:00:00 północy.

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

### <a name="use-c-expressions-for-todays-date"></a>Użyj wyrażeń języka C# dla bieżącej daty

Aby ściągnąć bieżącą datę, możemy użyć następującego wyrażenia języka C#: `DateTime.Now.ToString("M/d/yyyy")`

Oto przykład sposobu użycia tego wyrażenia w skrypcie:

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
## <a name="using-net-assemblies"></a>Używanie zestawów platformy .NET

Model rozszerzalności U-SQL rolę odgrywa możliwość dodawania niestandardowego kodu z zestawów platformy .NET. 

### <a name="register-a-net-assembly"></a>Rejestrowanie zestawów platformy .NET

Użyj `CREATE ASSEMBLY` instrukcję, aby umieścić zestaw .NET do bazy danych U-SQL. Po tym dniu skryptów U-SQL można używać tych zestawów za pomocą `REFERENCE ASSEMBLY` instrukcji. 

Poniższy kod pokazuje, jak można zarejestrować zestawu:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Poniższy kod pokazuje, jak odwołać się do zestawu:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Zapoznaj się z [instrukcje rejestracji zestawu](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) , opisano w tym temacie bardziej szczegółowo.


### <a name="use-assembly-versioning"></a>Użyj przechowywanie wersji zestawu
Obecnie U-SQL używa .NET Framework w wersji 4.5. Więc upewnij się, że własne zestawy są zgodne z danej wersji środowiska uruchomieniowego.

Jak wspomniano wcześniej, U-SQL uruchomienie kodu w formacie 64-bitowych (x 64). Dlatego upewnij się, że Twój kod jest kompilowany do uruchamiania na x64. W przeciwnym razie zostanie wyświetlony błąd niepoprawny format przedstawionej wcześniej.

Każdy przekazany zestawu biblioteki DLL i pliku zasobów, takich jak różne środowiska uruchomieniowego, natywny zestaw lub plik konfiguracji może mieć maksymalnie 400 MB. Całkowity rozmiar wdrożonych zasobów za pomocą wdrażania zasobów lub za pomocą odwołania do zestawów i ich dodatkowych plików, nie może przekroczyć 3 GB.

Na koniec należy pamiętać, że każda baza danych U-SQL może zawierać tylko jedną wersję dowolnego danego zestawu. Na przykład jeśli potrzebujesz, zarówno w wersji 7 i 8 wersję biblioteki NewtonSoft Json.NET, należy zarejestrować je w dwóch różnych bazach danych. Ponadto każdy skrypt może odwoływać się tylko do jednej wersji danego zestawu biblioteki DLL. W związku z tym U-SQL używa semantyki C# zestawu zarządzania i przechowywania wersji.

## <a name="use-user-defined-functions-udf"></a>Użyj funkcji zdefiniowanych przez użytkownika: UDF
Funkcje zdefiniowane przez użytkownika języka U-SQL lub funkcji definiowanych przez użytkownika, programowania procedur, które akceptują parametry, wykonania akcji (na przykład skomplikowanego obliczenia) i zwracają wynik tego działania jako wartość. Wartość zwracana funkcji zdefiniowanej przez użytkownika może być tylko jednego skalarną. UDF języka U-SQL może zostać wywołany w podstawowej skrypt U-SQL, takich jak dowolnego innego języka C# funkcji skalarnej.

Firma Microsoft zaleca inicjowania funkcje zdefiniowane przez użytkownika języka U-SQL jako **publicznych** i **statyczne**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Pierwszy Przyjrzyjmy się prosty przykład tworzenia funkcji zdefiniowanej przez użytkownika.

W tym scenariuszu przypadek użycia należy ustalić okres, w tym kwartał obrachunkowy i miesiąc obrachunkowy pierwszego logowania dla określonego użytkownika. Pierwszy miesiąc obrachunkowy roku w naszym scenariuszu jest czerwca.

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

Po prostu oblicza miesiąc obrachunkowy i kwartał i zwraca wartość ciągu. W czerwcu pierwszy miesiąc pierwszego kwartału obrachunkowego, korzystamy z "Q1:P1". Lipca możemy użyć "Q1:P2" i tak dalej.

Jest to zwykły funkcji C#, firma Microsoft ma być używany w projekcie języka U-SQL.

Oto jak wygląda sekcji związanym z kodem w tym scenariuszu:

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

Teraz użyjemy do wywołania tej funkcji z podstawowej skrypt U-SQL. Aby to zrobić, mamy Podaj w pełni kwalifikowaną nazwę funkcji, włącznie z przestrzenią nazw, czyli w tym przypadku NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Poniżej przedstawiono rzeczywiste podstawowy skrypt U-SQL:

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

Poniżej znajduje się plik wyjściowy wykonywanie skryptu:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Ten przykład pokazuje prosty sposób użycia klasy wbudowanej funkcji UDF w języku U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Zachowania stanu między wywołań funkcji zdefiniowanej przez użytkownika
Obiekty programowania U-SQL C# można być bardziej zaawansowane, a wykorzystujących interakcyjność za pomocą zmiennych globalnych związanym z kodem. Przyjrzyjmy się następujące scenariusza przypadków użycia biznesowego.

W dużych organizacjach użytkowników można przełączać się między różne typy aplikacji wewnętrznych. Obejmują one Microsoft Dynamics CRM, Power Bi i tak dalej. Klienci powinni zastosować analizę danych telemetrycznych jak użytkownicy będą przełączać się między różnymi aplikacjami, jakie są trendy użycia, i tak dalej. Celem firmy jest aby zoptymalizować użycie aplikacji. Może być do łączenia różnych aplikacjach lub określonych procedur logowania jednokrotnego.

Aby osiągnąć ten cel, musimy określić identyfikatory sesji i zwłoki czas od ostatniej sesji, który wystąpił.

Potrzebujemy znaleźć poprzedniego logowania i następnie przypisz tego zaloguj się do wszystkich sesji, które są generowane w tej samej aplikacji. Pierwsze wyzwanie polega na podstawowy skrypt U-SQL nie dopuszcza nam zastosowanie obliczenia za pośrednictwem już obliczana kolumn za pomocą funkcji LAG. Drugie żądanie jest firma Microsoft zapewnienie określonej sesji dla wszystkich sesji, w tym samym przedziale czasu.

Aby rozwiązać ten problem, używamy zmienną globalną wewnątrz sekcji związanym z kodem: `static public string globalSession;`.

Ta zmienna globalna jest stosowane do całego zestawu wierszy podczas wykonywania naszego skryptu.

Poniżej przedstawiono sekcję związanym z kodem nasz program U-SQL:

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

Ten przykład przedstawia zmienną globalną `static public string globalSession;` używana wewnątrz `getStampUserSession` funkcji i wprowadzenie ponownie zainicjować każdorazowo parametr sesji zostanie zmieniony.

Podstawowy skrypt U-SQL jest w następujący sposób:

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

Funkcja `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` jest wywoływana w tym miejscu podczas obliczania drugiego zestawu wierszy pamięci. Przekazuje ono `UserSessionTimestamp` kolumny i zwraca wartość, dopóki `UserSessionTimestamp` został zmieniony.

Plik wyjściowy jest następująca:

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

W przykładzie pokazano bardziej skomplikowanych scenariuszy przypadków użycia, w której używamy zmienną globalną wewnątrz sekcji związanym z kodem, które są stosowane do całej pamięci zestawu wierszy.

## <a name="use-user-defined-types-udt"></a>Użyj typów zdefiniowanych przez użytkownika: UDT
Typy zdefiniowane przez użytkownika lub UDT, jest inna funkcja programowania U-SQL. UDT U-SQL zachowuje się jak zwykły typ C# zdefiniowanych przez użytkownika. C# to silnie typizowany język, który umożliwia korzystanie z wbudowanych i niestandardowych typów zdefiniowanych przez użytkownika.

U-SQL nie można niejawnie serializować lub deserializować dowolnych typów zdefiniowanych przez użytkownika, gdy UDT zostaną przekazane między wierzchołków zestawów wierszy. Oznacza to, że użytkownik będzie musiał podać jawne elementu formatującego za pomocą interfejs IFormatter. To zapewnia serializacja U-SQL i deserializować metod dla typu.

> [!NOTE]
> U-SQL wbudowane, ekstraktory i outputters obecnie nie można serializować lub deserializować UDT danych do i z plików, nawet w przypadku zestawu IFormatter. Dlatego podczas pisania UDT danych do pliku przy użyciu instrukcji w danych wyjściowych, lub wczytaniem go przy użyciu ekstraktora, należy przekazać go jako ciągu lub tablicy typu byte. Następnie wywołaj serializacji i deserializacji kodu (czyli metody ToString() UDT) jawnie. Zdefiniowane przez użytkownika modułów wyodrębniających i outputters, z drugiej strony, można odczytu i zapisu typów zdefiniowanych przez użytkownika.

Jeśli firma Microsoft spróbuj użyć UDT EKSTRAKTOR lub OUTPUTTER (z poprzedniego wybierz), jak pokazano poniżej:

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

Aby pracować z UDT w outputter, albo mamy do ciągu za pomocą metody ToString() lub utworzyć niestandardowe outputter serializacji.

Typów zdefiniowanych przez użytkownika nie można obecnie używać GROUP BY. Jeśli UDT służy GROUP BY, zostanie zgłoszony następujący błąd:

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

* Dodaj następujące przestrzenie nazw:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Dodaj `Microsoft.Analytics.Interfaces`, co jest wymagane dla interfejsów UDT. Ponadto `System.IO` mogą być wymagane, aby zdefiniować interfejs IFormatter.

* Definiowanie typu zdefiniowane za pomocą atrybutu SqlUserDefinedType.

**SqlUserDefinedType** służy do oznaczania definicję typu w zestawie jako typ zdefiniowany przez użytkownika (UDT) w języku U-SQL. Właściwości w atrybucie odzwierciedlają właściwości fizyczne UDT. Ta klasa nie może być dziedziczona.

SqlUserDefinedType jest wymaganego atrybutu dla definicji UDT.

Konstruktor klasy:  

* SqlUserDefinedTypeAttribute (elementu formatującego typu)

* Element formatujący typu: Wymagany parametr do zdefiniowania elementu formatującego UDT — w szczególności typ `IFormatter` interfejsu muszą być przekazywane w tym miejscu.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typowe UDT wymaga również definicji interfejs IFormatter, jak pokazano w poniższym przykładzie:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter` Interfejsu serializuje i deserializuje z wykresu obiektu z typem głównym \<typeparamref name = "T" >.

\<typeparam name = "T" > główny typ wykresu obiektu do serializacji i deserializacji.

* **Deserializacji**: Deserializuje dane na podany strumień i reconstitutes grafu obiektów.

* **Serializowanie**: Serializuje obiekt lub grafu obiektów z danym elementem głównym do podanego strumienia.

`MyType` Wystąpienie: Wystąpienie tego typu.  
`IColumnWriter` Moduł zapisujący / `IColumnReader` czytnik: Zasadniczy strumień kolumny.  
`ISerializationContext` Kontekst: Wyliczenie, który definiuje zestaw flag, który określa kontekst źródle lub miejscu docelowym dla strumienia podczas serializacji.

* **Pośredni**: Określa, czy kontekst źródle lub miejscu docelowym nie jest magazynu trwałego.

* **Trwałość**: Określa, że kontekst źródle lub miejscu docelowym magazynu trwałego.

Jako zwykły typ C#, definicji UDT U-SQL może zawierać zastąpień dla operatorów takich jak +/ == /! =. Może również obejmować metod statycznych. Na przykład, jeśli są użyjemy tego UDT jako parametr do funkcji agregujących MIN U-SQL, mamy do definiowania < zastąpienia operatora.

We wcześniejszej części tego przewodnika firma Microsoft przedstawiono przykład obrachunkowy identyfikacji okresu od określonej daty w formacie `Qn:Pn (Q1:P10)`. Poniższy przykład pokazuje jak zdefiniować typ niestandardowy obrachunkowy wartości okresu.

Poniżej znajduje się przykład sekcji związanym z kodem za pomocą niestandardowego interfejsu UDT i IFormatter:

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

Zdefiniowanego typu zawiera dwie liczby: kwartał i miesiąc. Operatory `==/!=/>/<` i metody statycznej `ToString()` są zdefiniowane w tym miejscu.

Jak wspomniano wcześniej, można używać w wyrażeniach wybierz UDT, ale nie można używać w OUTPUTTER/EKSTRAKTOR bez niestandardowej serializacji. Albo musi być serializowana w postaci ciągu z `ToString()` lub niestandardowy OUTPUTTER/moduł WYODRĘBNIAJĄCY.

Teraz omówimy użycia UDT. W sekcji związanym z kodem zmieniliśmy nasze funkcja GetFiscalPeriod do następujących:

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

W tym miejscu udostępniamy przykład jak używać ich dalszą w podstawowej skrypt U-SQL. Ten przykład demonstruje różne rodzaje tego wywołania UDT ze skryptu U-SQL.

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

Oto przykład sekcji pełną związanym z kodem:

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
Agregacje zdefiniowane przez użytkownika są wszystkie funkcje związane z agregacji, które są nie są dostarczane out-of--box przy użyciu języka U-SQL. Przykład może być agregacją do wykonywania obliczeń matematycznych niestandardowe, konkatenacji ciągów, manipulacje za pomocą ciągów i tak dalej.

Definicja użytkownika agregacji klasy bazowej jest w następujący sposób:

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

**SqlUserDefinedAggregate** wskazuje, że typ powinien być zarejestrowany jako agregacja zdefiniowana przez użytkownika. Ta klasa nie może być dziedziczona.

Atrybut SqlUserDefinedType jest **opcjonalne** UDAGG definicji.


Klasa bazowa umożliwia przekazywanie trzy parametry abstrakcyjnej: dwa jako parametry wejściowe, a drugi jako wynik. Typy danych są zmienne i powinien być zdefiniowany podczas dziedziczenia klas.

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

* **Init** podczas obliczania, a następnie wywołuje jeden raz dla każdej grupy. Procedura inicjowania zapewnia dla każdej grupy agregacji.  
* **Accumulate** jest wykonywane raz dla każdej wartości. Udostępnia ona funkcje głównego dla algorytmu agregacji. Może służyć do wartości zagregowanych z różnymi typami danych, które są zdefiniowane podczas dziedziczenia klas. Może akceptować dwóch parametrów typów danych zmiennej.
* **Zakończenie** jest wykonywane raz dla każdej grupy agregacji po zakończeniu przetwarzania w danych wyjściowych wyników dla każdej grupy.

Aby zadeklarować poprawne dane wejściowe i typów danych wyjściowych, należy użyć definicji klasy w następujący sposób:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Pierwszy parametr do
* T2: Drugi parametr do
* TResult: Zwracany typ zakończenia

Na przykład:

```
public class GuidAggregate : IAggregate<string, int, int>
```

lub

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Użyj UDAGG w języku U-SQL
Aby użyć UDAGG, zdefiniuj go w związanym z kodem lub odwołanie do niego z programowania celowe DLL zgodnie z wcześniejszym opisem.

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

I oprzeć skrypt U-SQL:

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

W tym scenariuszu przypadek użycia możemy łączenie klasy identyfikatorów GUID dla konkretnych użytkowników.

## <a name="use-user-defined-objects-udo"></a>Za pomocą obiektów zdefiniowanych przez użytkownika: UDO
U-SQL umożliwia zdefiniowanie programowania niestandardowe obiekty, które są nazywane obiekty zdefiniowane przez użytkownika lub operatora zdefiniowanego przez użytkownika.

Oto lista operatory zdefiniowane przez użytkownika w języku U-SQL:

* Zdefiniowane przez użytkownika ekstraktory
    * Wyodrębnij wiersz po wierszu
    * Używany do implementowania wyodrębnianie danych z niestandardowych plików ze strukturą

* Outputters zdefiniowanych przez użytkownika
    * Wiersz po wierszu danych wyjściowych
    * Umożliwia danych wyjściowych niestandardowych typów danych lub pliku niestandardowych formatów

* Procesory zdefiniowanych przez użytkownika
    * Jeden wiersz i tworzące jeden wiersz
    * Zmniejsz liczbę kolumn lub tworzyć nowe kolumny z wartościami, które są uzyskiwane z istniejącego zestawu kolumn

* Appliers zdefiniowanych przez użytkownika
    * Jeden wiersz i tworzące 0, n wiersze
    * Używane z Zastosuj zewnętrzne/UKOŚNEJ

* Combiners zdefiniowanych przez użytkownika
    * Łączy zestawów wierszy — sprzężenia zdefiniowanych przez użytkownika

* Reduktorów zdefiniowanych przez użytkownika
    * N wierszy i tworzące jeden wiersz
    * Pozwala zmniejszyć liczbę wierszy

Operatory zdefiniowane przez użytkownika jest zwykle nazywany jawnie w skrypcie U-SQL w ramach następujących instrukcji języka U-SQL:

* EXTRACT
* DANE WYJŚCIOWE
* PROCES
* COMBINE
* ZMNIEJSZ

> [!NOTE]  
> Operatory zdefiniowane przez tego użytkownika są ograniczone użycie 0,5 Gb pamięci RAM.  To ograniczenie pamięci nie ma zastosowania do lokalnych wykonań.

## <a name="use-user-defined-extractors"></a>Użyj zdefiniowanych przez użytkownika ekstraktory
U-SQL umożliwia importowanie danych zewnętrznych przy użyciu instrukcji WYODRĘBNIANIA. Instrukcja WYODRĘBNIANIA można użyć wbudowanych ekstraktory operatory zdefiniowane przez użytkownika:  

* *Extractors.Text()*: Udostępnia wyodrębniania z rozdzielanych plików tekstowych różne kodowania.

* *Extractors.Csv()*: Zapewnia wyodrębniania z wartości rozdzielanych przecinkami (CSV) plików różnych kodowań.

* *Extractors.Tsv()*: Udostępnia wyodrębniania wartości rozdzielane znakami tabulacji pliki (TSV) z różnych kodowań.

Może być przydatne do tworzenia niestandardowy moduł wyodrębniający. Może to być przydatne podczas importowania danych, jeśli chcemy wykonać jedną z następujących zadań:

* Modyfikowanie danych wejściowych, dzielenie kolumn i modyfikując poszczególne wartości. Funkcja PROCESORA jest lepszym rozwiązaniem dla łączenie kolumn.
* Analizowanie danych bez struktury, takich jak strony sieci Web i wiadomości e-mail lub częściowo pozbawionych struktury danych, takich jak XML/JSON.
* Analizowanie danych przy użyciu kodowania nieobsługiwany.

Aby zdefiniować wyodrębniania zdefiniowanych przez użytkownika lub LUCZ, musimy utworzyć `IExtractor` interfejsu. Wszystkie parametry do wyodrębniania, takich jak ograniczników wiersza/kolumny wejściowe i kodowanie, muszą być zdefiniowane w konstruktorze klasy. `IExtractor` Interfejs powinien również zawierać definicję `IEnumerable<IRow>` zastąpienia w następujący sposób:

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

**SqlUserDefinedExtractor** atrybut wskazuje, że typ powinien być zarejestrowany jako ekstraktor zdefiniowanych przez użytkownika. Ta klasa nie może być dziedziczona.

SqlUserDefinedExtractor jest opcjonalny atrybut LUCZ definicji. Go używać do definiowania AtomicFileProcessing właściwości dla obiektu LUCZ.

* bool     AtomicFileProcessing   

* **wartość true,** = wskazuje, że ta ekstraktor wymaga atomic plików wejściowych (JSON, XML,...)
* **FALSE** = wskazuje, że ta ekstraktor poradzenie sobie z plików dzielenie / rozproszonej (CSV, SEQ...)

Obiekty główne programowania LUCZ są **wejściowych** i **dane wyjściowe**. Wejściowy obiekt jest używany do wyliczenia danych wejściowych jako `IUnstructuredReader`. Obiekt danych wyjściowych jest używany do zestawu danych wyjściowych w wyniku działania ekstraktor.

W danych wejściowych jest dostępny za pośrednictwem `System.IO.Stream` i `System.IO.StreamReader`.

Kolumny wejściowe wyliczenia możemy najpierw dzielenie strumienia wejściowego za pomocą ogranicznik wiersza.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

W efekcie dodatkowo podzielić wiersz danych wejściowych części kolumny.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Aby ustawić dane wyjściowe, użyjemy `output.Set` metody.

Jest ważne dowiedzieć się, że niestandardowy moduł wyodrębniający tylko danych wyjściowych kolumn i wartości, które są zdefiniowane z danych wyjściowych. Ustaw wywołania metody.

```
output.Set<string>(count, part);
```

Ekstraktor rzeczywistych danych wyjściowych jest wyzwalany przez wywołanie metody `yield return output.AsReadOnly();`.

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

W tym scenariuszu przypadek użycia ekstraktor generuje identyfikator GUID dla kolumny "guid" i konwertuje wartości kolumny "user" na wielkie litery. Niestandardowe ekstraktory może wygenerować wyniki bardziej skomplikowane, przez analizowanie danych wejściowych i manipulowanie nimi go.

Poniżej przedstawiono podstawowy skrypt U-SQL, który używa niestandardowy moduł wyodrębniający:

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

## <a name="use-user-defined-outputters"></a>Użyj outputters zdefiniowanych przez użytkownika
Outputter zdefiniowanych przez użytkownika jest innego języka U-SQL operatory zdefiniowane przez użytkownika, który umożliwia rozszerzanie wbudowanych funkcji języka U-SQL. Podobnie jak wyodrębnianie, istnieje kilka wbudowanych outputters.

* *Outputters.Text()*: Zapisuje dane do rozdzielanych plików tekstowych różnych kodowań.
* *Outputters.Csv()*: Zapisuje dane do plików różnych kodowań rozdzielanymi przecinkami (CSV).
* *Outputters.Tsv()*: Zapisuje dane do plików różnych kodowań wartości rozdzielane tabulatorami (TSV).

Outputter niestandardowych umożliwia zapisywanie danych w niestandardowym formacie zdefiniowane. Może to być przydatne w przypadku następujących zadań:

* Zapisywanie danych w plikach z częściową strukturą lub bez struktury.
* Zapisywanie danych nie obsługuje kodowania.
* Modyfikowanie danych wyjściowych lub dodawanie atrybutów niestandardowych.

Aby zdefiniować outputter zdefiniowanych przez użytkownika, należy utworzyć `IOutputter` interfejsu.

Poniżej przedstawiono podstawowy `IOutputter` Implementacja klasy:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Wszystkie parametry wejściowe outputter, takie jak ograniczników wiersza/kolumny, kodowanie i tak dalej, muszą być zdefiniowane w konstruktorze klasy. `IOutputter` Interfejs powinien również zawierać definicję `void Output` zastąpienia. Ten atrybut `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` Opcjonalnie można ustawić dla przetwarzania plików atomic. Aby uzyskać więcej informacji zobacz następujące szczegóły.

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

* `Output` jest wywoływana dla każdego wiersza danych wejściowych. Zwraca `IUnstructuredWriter output` zestawu wierszy.
* Klasa konstruktora jest używana w celu przekazania parametrów do outputter zdefiniowanych przez użytkownika.
* `Close` Umożliwia opcjonalnie należy przesłonić, aby zwolnić kosztowne stanu lub określić, kiedy ostatni wiersz został napisany.

**SqlUserDefinedOutputter** atrybut wskazuje, że typ powinien być zarejestrowany jako outputter zdefiniowanych przez użytkownika. Ta klasa nie może być dziedziczona.

SqlUserDefinedOutputter jest opcjonalny atrybut definicji outputter zdefiniowanych przez użytkownika. Służy do definiowania właściwości AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **wartość true,** = wskazuje, że ta outputter wymaga pliki wyjściowe niepodzielne (JSON, XML,...)
* **FALSE** = wskazuje, że to outputter poradzenie sobie z plikami dzielenie / rozproszonej (CSV, SEQ...)

Obiekty główne programowania są **wiersz** i **dane wyjściowe**. **Wiersz** obiekt jest używany do wyliczenia dane wyjściowe jako `IRow` interfejsu. **Dane wyjściowe** służy do ustawiania dane wyjściowe do pliku docelowego.

Dane wyjściowe jest dostępny za pośrednictwem `IRow` interfejsu. Dane wyjściowe są przekazywane wiersz w danym momencie.

Poszczególne wartości wyliczane są przez wywołanie metody Get interfejsu IRow:

```
row.Get<string>("column_name")
```

Można określić nazwy poszczególnych kolumn, wywołując `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Takie podejście umożliwia tworzenie elastycznych outputter dla dowolnego schematu metadanych.

Dane wyjściowe są zapisywane do pliku za pomocą `System.IO.StreamWriter`. Parametr strumienia jest ustawiony na `output.BaseStream` jako część `IUnstructuredWriter output`.

Należy pamiętać, że ważne jest, aby opróżniania buforu danych do pliku po każdej iteracji wiersza. Ponadto `StreamWriter` atrybutem możliwe do rozporządzania włączone (ustawienie domyślne) i za pomocą, można użyć obiektu **przy użyciu** — słowo kluczowe:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

W przeciwnym razie metoda Flush() jawnie wywołać po każdej iteracji. Pokazano to w poniższym przykładzie.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ustaw nagłówki i stopki dla outputter zdefiniowanych przez użytkownika
Aby ustawić nagłówek, użyj przepływu wykonanie jednej iteracji.

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

Stopki, użyj odwołania do wystąpienia `System.IO.Stream` obiektu (`output.BaseStream`). Zapis stopki w ramach metody Close() `IOutputter` interfejsu.  (Aby uzyskać więcej informacji, zobacz poniższy przykład).

Poniżej przedstawiono przykład outputter zdefiniowanych przez użytkownika:

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

Jest to outputter HTML, który tworzy plik HTML przy użyciu danych z tabeli.

### <a name="call-outputter-from-u-sql-base-script"></a>Wywoływanie outputter z podstawowej skrypt U-SQL
Aby wywołać outputter niestandardowe z podstawowej skrypt U-SQL, nowe wystąpienie obiektu outputter musi być utworzony.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Aby uniknąć tworzenia wystąpienia obiektu w skrypcie podstawowej, możemy utworzyć otokę funkcji zgodnie z naszym wcześniejszym przykładzie:

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

W tym przypadku oryginalne wywołanie wygląda podobnie do poniższego:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Używają procesorów zdefiniowanych przez użytkownika
Procesor zdefiniowanych przez użytkownika lub UDP, jest typem operatory języka U-SQL zdefiniowane przez użytkownika, który umożliwia przetwarzanie przychodzących wierszy, stosując funkcje programowania. UDP umożliwia łączenie kolumn, zmodyfikuj wartości i dodać nowe kolumny, jeśli to konieczne. Po prostu pomaga przetworzyć zestawu wierszy do produkcji elementów wymaganych danych.

Aby zdefiniować UDP, musimy utworzyć `IProcessor` współpracować z usługą `SqlUserDefinedProcessor` atrybut, który jest opcjonalny w przypadku protokołu UDP.

Ten interfejs może zawierać definicję `IRow` zastąpić interfejsu zestawu wierszy, jak pokazano w poniższym przykładzie:

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

**SqlUserDefinedProcessor** wskazuje, że typ powinien być zarejestrowany jako podmiot przetwarzający zdefiniowanych przez użytkownika. Ta klasa nie może być dziedziczona.

Atrybut SqlUserDefinedProcessor jest **opcjonalne** definicji protokołu UDP.

Obiekty główne programowania są **wejściowych** i **dane wyjściowe**. Wejściowy obiekt jest używany, można wyliczyć kolumny wejściowe i wyjściowe oraz ustawić dane wyjściowe wyniku aktywności procesora.

Wyliczenia kolumny wejściowe używamy `input.Get` metody.

```
string column_name = input.Get<string>("column_name");
```

Parametr `input.Get` metody to kolumna, która jest przekazywany jako część `PRODUCE` klauzuli `PROCESS` instrukcji podstawowy skrypt U-SQL. Należy korzystać w poprawnym typie danych.

Dla danych wyjściowych, należy użyć `output.Set` metody.

Należy pamiętać, że niestandardowe producentów wyświetla tylko kolumn i wartości, które są zdefiniowane przy użyciu `output.Set` wywołania metody.

```
output.Set<string>("mycolumn", mycolumn);
```

Rzeczywisty procesor danych wyjściowych jest wyzwalany przez wywołanie metody `return output.AsReadOnly();`.

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

W tym scenariuszu przypadek użycia procesora generuje nową kolumnę o nazwie "full_description", łącząc istniejących kolumn — w tym przypadku "user", wielkie litery i "des". Również generuje identyfikator GUID i zwraca oryginalne i nowe wartości identyfikatora GUID.

Jak widać w poprzednim przykładzie, można wywołać metodę języka C# podczas `output.Set` wywołania metody.

Oto przykład podstawowej skrypt U-SQL, który używa niestandardowego procesora:

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

## <a name="use-user-defined-appliers"></a>Użyj appliers zdefiniowanych przez użytkownika
Applier zdefiniowanych przez użytkownika języka U-SQL umożliwia wywoływanie niestandardowych funkcji C# dla każdego wiersza, który jest zwracany przez wyrażenie zewnętrzne tabeli zapytania. Odpowiednie dane wejściowe jest oceniana dla każdego wiersza wejściowego po lewej stronie, a wiersze, które są tworzone są łączone na końcowych danych wyjściowych. Lista kolumn, które są produkowane przez APPLY operator są kombinacją zestawu kolumn w lewo i prawo dane wejściowe.

Jako część wyrażenia skryptu u-SQL ZAZNACZYĆ wywoływana jest applier zdefiniowanych przez użytkownika.

Typowe wywołanie applier zdefiniowanych przez użytkownika wygląda podobnie do poniższego:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Aby uzyskać więcej informacji o używaniu appliers w wyrażeniu wybierz zobacz [U-SQL ZAZNACZYĆ zaznaczając CROSS APPLY a operatora OUTER APPLY](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Zdefiniowane przez użytkownika, applier definicji klasy bazowej jest następująca:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Aby zdefiniować applier zdefiniowanych przez użytkownika, należy utworzyć `IApplier` współpracować z usługą [`SqlUserDefinedApplier`] atrybut, który jest opcjonalny w przypadku definicji applier zdefiniowanych przez użytkownika.

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

* Zastosuj jest wywoływana dla każdego wiersza tabeli zewnętrznej. Zwraca `IUpdatableRow` dane wyjściowe zestawu wierszy.
* Klasa konstruktora jest używana w celu przekazania parametrów do applier zdefiniowanych przez użytkownika.

**SqlUserDefinedApplier** wskazuje, że typ powinien być zarejestrowany jako applier zdefiniowanych przez użytkownika. Ta klasa nie może być dziedziczona.

**SqlUserDefinedApplier** jest **opcjonalne** dla definicji applier zdefiniowanych przez użytkownika.


Obiekty główne programowania są w następujący sposób:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Wejściowe zestawy wierszy są przekazywane jako `IRow` danych wejściowych. Wiersze danych wyjściowych są generowane jako `IUpdatableRow` interfejs danych wyjściowych.

Można określić nazwy poszczególnych kolumn, wywołując `IRow` metoda schematu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Aby uzyskać wartości rzeczywiste dane z przychodzącego `IRow`, możemy użyć metody Get() `IRow` interfejsu.

```
mycolumn = row.Get<int>("mycolumn")
```

Lub używamy nazwy kolumn schematu:

```
row.Get<int>(row.Schema[0].Name)
```

Należy ustawić wartości danych wyjściowych z `IUpdatableRow` dane wyjściowe:

```
output.Set<int>("mycolumn", mycolumn)
```

Jest ważne dowiedzieć się, że niestandardowe appliers tylko dane wyjściowe kolumn i wartości, które są zdefiniowane przy użyciu `output.Set` wywołania metody.

Rzeczywiste dane wyjściowe zostanie wywołany przez wywołanie metody `yield return output.AsReadOnly();`.

Parametry applier zdefiniowanych przez użytkownika mogą być przekazywane do konstruktora. Applier może zwracać zmienną liczbę kolumn, które muszą być zdefiniowane w trakcie połączenia uwierzytelniającego applier w podstawowej skrypt U-SQL.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Oto przykład applier zdefiniowanych przez użytkownika:

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

Poniżej przedstawiono podstawowy skrypt U-SQL dla tego applier zdefiniowanych przez użytkownika:

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

W tym scenariuszami przypadków użycia applier zdefiniowanych przez użytkownika działa jako analizator rozdzielanym przecinkami dla właściwości floty samochodów. Wiersze z pliku wejściowego wyglądać następująco:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Jest to typowy rozdzielane tabulatorami TSV plik z kolumną właściwości, który zawiera właściwości samochód, takich jak marka i model. Te właściwości należy przeanalizować do kolumn tabeli. Applier, który znajduje się umożliwia także wygenerować dynamiczne liczby właściwości w zestawie wierszy wyników, na podstawie parametru, który jest przekazywany. Można wygenerować wszystkich właściwości lub zbiór tylko właściwości.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Applier zdefiniowanych przez użytkownika mogą być wywoływane jako nowe wystąpienie obiektu applier:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Lub za pomocą wywołania metody fabryki otoki:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Użyj combiners zdefiniowanych przez użytkownika
Zdefiniowane przez użytkownika funkcja łączenia lub UDC, umożliwia łączenie wiersze z lewego i prawego zestawów wierszy, oparte na niestandardowej logiki. Zdefiniowane przez użytkownika używana jest funkcja łączenia za pomocą wyrażenia łączenia.

Funkcja łączenia jest wywoływana za pomocą wyrażenia łączenia, który dostarcza niezbędne informacje o zarówno wejściowych zestawów wierszy, kolumny grupowania, oczekiwany wynik schematu i dodatkowe informacje.

Aby wywołać łączenia w podstawowej skrypt U-SQL, możemy użyj następującej składni:

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

Aby uzyskać więcej informacji, zobacz [POŁĄCZYĆ wyrażenia (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Aby zdefiniować funkcja łączenia zdefiniowana przez użytkownika, należy utworzyć `ICombiner` współpracować z usługą [`SqlUserDefinedCombiner`] atrybut, który jest opcjonalny w przypadku definicji funkcja łączenia zdefiniowana przez użytkownika.

Podstawa `ICombiner` definicję klasy:

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

Niestandardową implementację `ICombiner` interfejs musi zawierać definicję parametrów dla `IEnumerable<IRow>` połączyć zastąpienie.

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

**SqlUserDefinedCombiner** atrybut wskazuje, że typ powinien być zarejestrowany jako funkcja łączenia zdefiniowana przez użytkownika. Ta klasa nie może być dziedziczona.

**SqlUserDefinedCombiner** służy do definiowania właściwości trybu funkcji łączenia. Jest opcjonalny atrybut definicji łączenia zdefiniowana przez użytkownika.

Tryb CombinerMode

Wyliczenie CombinerMode może przyjmować następujące wartości:

* Pełne (0), każdy wiersz danych wyjściowych potencjalnie jest zależna od wszystkich wierszy danych wejściowych od lewej i prawej z taką samą wartość klucza.

* Po lewej stronie (1) każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz danych wejściowych z lewej strony (i potencjalnie wszystkie wiersze z prawej strony z taką samą wartość klucza).

* Po prawej stronie (2) każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz danych wejściowych z prawej strony (i potencjalnie wszystkie wiersze z lewej strony z taką samą wartość klucza).

* Element wewnętrzny (3) każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz danych wejściowych od lewej i prawej z taką samą wartość.

Przykład: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Obiekty główne programowania są:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Wejściowe zestawy wierszy są przekazywane jako **po lewej stronie** i **prawo** `IRowset` typ interfejsu. Oba zestawy wierszy należy wyliczyć do przetworzenia. Można tylko wyliczyć każdego interfejsu, dzięki czemu będziemy mogli mógł wyliczyć i Buforuj go, jeśli to konieczne.

Na potrzeby buforowania, można utworzyć listy\<T\> typ struktury pamięci w wyniku LINQ wykonywanie zapytania, w szczególności listy <`IRow`>. Typ anonimowy danych może służyć podczas wyliczania, jak również.

Zobacz [wprowadzenie do zapytań LINQ (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) Aby uzyskać więcej informacji o zapytaniach LINQ i [IEnumerable\<T\> interfejsu](/dotnet/api/system.collections.generic.ienumerable-1) Aby uzyskać więcej informacji na temat interfejsu IEnumerable\<T\> interfejsu.

Aby uzyskać wartości rzeczywiste dane z przychodzącego `IRowset`, możemy użyć metody Get() `IRow` interfejsu.

```
mycolumn = row.Get<int>("mycolumn")
```

Można określić nazwy poszczególnych kolumn, wywołując `IRow` metoda schematu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Lub za pomocą nazwy kolumn schematu:

```
c# row.Get<int>(row.Schema[0].Name)
```

Ogólne wyliczenia za pomocą LINQ wygląda podobnie do poniższego:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Po wyliczanie obu zestawów wierszy, użyjemy pętli wszystkich wierszy. Dla każdego wiersza w zestawie wierszy po lewej stronie zamierzamy Znajdź wszystkie wiersze, które spełniają warunek naszej funkcji łączenia.

Należy ustawić wartości danych wyjściowych z `IUpdatableRow` danych wyjściowych.

```
output.Set<int>("mycolumn", mycolumn)
```

Rzeczywiste dane wyjściowe jest wyzwalany przez wywołanie do `yield return output.AsReadOnly();`.

Poniżej przedstawiono przykład funkcji łączenia:

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

W tym scenariuszu przypadek użycia tworzymy raport analizy sprzedaży detalicznej. Celem jest, aby znaleźć wszystkie produkty, które koszt więcej niż 20 000 i który sprzedawać za pośrednictwem witryny internetowej szybciej niż za pomocą regularnego sprzedaży detalicznej w danym okresie.

Poniżej przedstawiono podstawowy skrypt U-SQL. Możesz porównać logika między sprzężenia regularnego i łączenia:

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

Funkcja łączenia zdefiniowana przez użytkownika mogą być wywoływane jako nowe wystąpienie obiektu applier:

```
USING new MyNameSpace.MyCombiner();
```


Lub za pomocą wywołania metody fabryki otoki:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Użyj reduktorów zdefiniowanych przez użytkownika

U-SQL umożliwia pisanie reduktorów niestandardowego zestawu wierszy w języku C# przy użyciu operatora zdefiniowanego przez użytkownika Struktura rozszerzalności i implementowanie interfejsu IReducer.

Reduktor zdefiniowany przez użytkownika lub trasa zdefiniowana przez użytkownika, można usunąć zbędne wiersze podczas wyodrębniania danych (Importuj). On również może służyć do manipulowania i ocenić wierszy i kolumn. Oparte na logiki programowania, może także definiować wiersze, które muszą zostać wyodrębnione.

Aby zdefiniować klasę trasy zdefiniowanej przez użytkownika, należy utworzyć `IReducer` interfejsu z opcjonalnymi `SqlUserDefinedReducer` atrybutu.

Ten interfejs klasy może zawierać definicję `IEnumerable` zastąpić interfejsu zestawu wierszy.

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

**SqlUserDefinedReducer** atrybut wskazuje, że typ powinien być zarejestrowany jako reduktor zdefiniowany przez użytkownika. Ta klasa nie może być dziedziczona.
**SqlUserDefinedReducer** jest opcjonalny atrybut definicji reduktor zdefiniowany przez użytkownika. Służy do definiowania właściwości IsRecursive.

* wartość logiczna IsRecursive    
* **wartość true,** = wskazuje, czy ten reduktor asocjacyjnych i przemiennego

Obiekty główne programowania są **wejściowych** i **dane wyjściowe**. Wejściowy obiekt jest używany do wyliczenia wiersze danych wejściowych. Dane wyjściowe jest używana do ustawiania wiersze danych wyjściowych w wyniku zmniejszenie działania.

W przypadku wyliczenia wiersze danych wejściowych, używamy `Row.Get` metody.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametr `Row.Get` metody to kolumna, która jest przekazywany jako część `PRODUCE` klasy `REDUCE` instrukcji podstawowy skrypt U-SQL. Musimy prawidłowy typ danych w tym miejscu użyć również.

Dla danych wyjściowych, należy użyć `output.Set` metody.

Jest ważne zrozumieć ten niestandardowy reduktor tylko generuje wartości wyjściowe, które są zdefiniowane przy użyciu `output.Set` wywołania metody.

```
output.Set<string>("mycolumn", guid);
```

Dane wyjściowe reduktor rzeczywisty jest wyzwalany przez wywołanie metody `yield return output.AsReadOnly();`.

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

W tym scenariuszu przypadek użycia reduktor pomija wiersze z nazwą użytkownika puste. Dla każdego wiersza w zestawie wierszy go odczytuje każdej wymaganej kolumny, a następnie oblicza długość nazwy użytkownika. Wyświetla rzeczywiste wiersza tylko wtedy, gdy długość nazwy użytkownika w wartości jest większa niż 0.

Poniżej przedstawiono podstawowy skrypt U-SQL, który używa niestandardowego reduktor:

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
