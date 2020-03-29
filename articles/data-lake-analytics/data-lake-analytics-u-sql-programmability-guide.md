---
title: Przewodnik po programowalności U-SQL dla usługi Azure Data Lake
description: Dowiedz się więcej o zestawie usług w usłudze Azure Data Lake Analytics, które umożliwiają tworzenie chmurowej platformy danych big data.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: dc55615d7a5c6ae9a393ed4fd5f49cd92aedc0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162579"
---
# <a name="u-sql-programmability-guide"></a>Przewodnik programowania U-SQL

U-SQL to język zapytań przeznaczony dla obciążeń typu danych big data. Jedną z unikatowych cech U-SQL jest połączenie języka deklaratywnego podobnego do JĘZYKA SQL z rozszerzalnością i programowalnością, która jest dostarczana przez język C#. W tym przewodniku koncentrujemy się na rozszerzalności i programowalność języka U-SQL, który jest włączony przez C#.

## <a name="requirements"></a>Wymagania

Pobierz i zainstaluj [narzędzia usługi Azure Data Lake Tools dla programu Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Wprowadzenie do języka U-SQL  

Spójrz na następujący skrypt U-SQL:

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

Ten skrypt definiuje dwa zestawy `@a` `@results`wierszy: i . Zestaw `@results` wierszy `@a`jest zdefiniowany z pliku .

## <a name="c-types-and-expressions-in-u-sql-script"></a>Typy i wyrażenia języka C# w skrypcie U-SQL

Wyrażenie U-SQL jest wyrażeniem języka C# połączonym `AND` `OR`z `NOT`operacjami logicznymi U-SQL, takimi jak , i . Wyrażenia U-SQL mogą być używane z SELECT, EXTRACT, WHERE, HAVING, GROUP BY i DECLARE. Na przykład następujący skrypt analizuje ciąg jako wartość DateTime.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Poniższy urywek analizuje ciąg jako datetime wartość w DECLARE instrukcji.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Używanie wyrażeń języka C# do konwersji typu danych

W poniższym przykładzie pokazano, jak można wykonać konwersję danych datetime przy użyciu wyrażeń języka C#. W tym konkretnym scenariuszu dane datetime ciągu ciąg jest konwertowany na standardowy datetime z północy 00:00:00 czas notacji.

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

### <a name="use-c-expressions-for-todays-date"></a>Używanie wyrażeń języka C# dla dzisiejszej daty

Aby wyciągnąć dzisiejszą datę, możemy użyć następującego wyrażenia C#:`DateTime.Now.ToString("M/d/yyyy")`

Oto przykład użycia tego wyrażenia w skrypcie:

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
## <a name="using-net-assemblies"></a>Korzystanie z zestawów .NET

Model rozszerzalności języka U-SQL w dużej mierze opiera się na możliwości dodawania kodu niestandardowego z zestawów .NET. 

### <a name="register-a-net-assembly"></a>Rejestrowanie zestawu .NET

Instrukcja `CREATE ASSEMBLY` służy do umieszczania zestawu .NET w bazie danych U-SQL. Następnie skrypty U-SQL można użyć tych `REFERENCE ASSEMBLY` zestawów przy użyciu instrukcji. 

Poniższy kod pokazuje, jak zarejestrować zestaw:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Poniższy kod pokazuje, jak odwoływać się do zestawu:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Zapoznaj się z [instrukcjami rejestracji zestawu,](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) które obejmują ten temat bardziej szczegółowo.


### <a name="use-assembly-versioning"></a>Korzystanie z przechowywania wersji zestawu
Obecnie U-SQL używa programu .NET Framework w wersji 4.5. Dlatego upewnij się, że własne zestawy są zgodne z tą wersją środowiska wykonawczego.

Jak wspomniano wcześniej, U-SQL uruchamia kod w formacie 64-bitowym (x64). Więc upewnij się, że kod jest skompilowany do uruchomienia na x64. W przeciwnym razie pojawi się błąd nieprawidłowego formatu wyświetlany wcześniej.

Każdy przekazany plik DLL zestawu i pliku zasobów, takich jak inny środowiska wykonawczego, natywnego zestawu lub pliku konfiguracyjnego, może mieć co najwyżej 400 MB. Całkowity rozmiar wdrożonych zasobów za pośrednictwem deploy resource lub za pośrednictwem odwołań do zestawów i ich dodatkowych plików nie może przekraczać 3 GB.

Na koniec należy zauważyć, że każda baza danych U-SQL może zawierać tylko jedną wersję danego zestawu. Na przykład jeśli potrzebujesz zarówno w wersji 7, jak i w wersji 8 biblioteki Json.NET NewtonSoft, musisz zarejestrować je w dwóch różnych bazach danych. Ponadto każdy skrypt może odwoływać się tylko do jednej wersji biblioteki DLL danego zestawu. W związku z tym U-SQL następuje c# zarządzania zestawami i semantyki wersji.

## <a name="use-user-defined-functions-udf"></a>Korzystanie z funkcji zdefiniowanych przez użytkownika: UDF
U-SQL funkcje zdefiniowane przez użytkownika lub UDF, są procedury programowania, które akceptują parametry, wykonać akcję (na przykład obliczenia złożone) i zwraca wynik tej akcji jako wartość. Zwracana wartość UDF może być tylko pojedyncza skalarna. U-SQL UDF można wywołać w skrypcie podstawowym U-SQL, jak każda inna funkcja skalarna języka C#.

Zaleca się zainicjowanie funkcji zdefiniowanych przez użytkownika U-SQL jako **publicznych** i **statycznych**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Najpierw przyjrzyjmy się prostemu przykładowi tworzenia UDF.

W tym scenariuszu użycia musimy określić okres obrachunkowy, w tym kwartał obrachunkowy i miesiąc obrachunkowy pierwszego logowania dla określonego użytkownika. Pierwszym miesiącem fiskalnym roku w naszym scenariuszu jest czerwiec.

Aby obliczyć okres obrachunkowy, wprowadzamy następującą funkcję Języka C#:

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

Po prostu oblicza miesiąc obrachunkowy i kwartał i zwraca wartość ciągu. W czerwcu, pierwszym miesiącu pierwszego kwartału fiskalnego, używamy "Q1:P1". W lipcu używamy "Q1:P2" i tak dalej.

Jest to zwykła funkcja języka C#, która będzie używana w naszym projekcie U-SQL.

Oto jak wygląda sekcja bez kodu w tym scenariuszu:

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

Teraz będziemy wywoływać tę funkcję z podstawowego skryptu U-SQL. Aby to zrobić, musimy podać w pełni kwalifikowaną nazwę dla funkcji, w tym obszar nazw, który w tym przypadku jest NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Poniżej znajduje się rzeczywisty skrypt podstawowy U-SQL:

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

Poniżej znajduje się plik wyjściowy wykonywania skryptu:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

W tym przykładzie pokazano proste użycie wbudowanego UDF w U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Zachowaj stan między wywołaniami UDF
Obiekty programowalności języka U-SQL C# mogą być bardziej zaawansowane, wykorzystując interaktywność za pośrednictwem zmiennych globalnych związanych z kodem. Przyjrzyjmy się następującego scenariusza przypadku użycia biznesowego.

W dużych organizacjach użytkownicy mogą przełączać się między odmianami aplikacji wewnętrznych. Mogą to być programy Microsoft Dynamics CRM, PowerBI itd. Klienci mogą chcieć zastosować analizę telemetryczną, jak użytkownicy przełączają się między różnymi aplikacjami, jakie są trendy użycia i tak dalej. Celem firmy jest optymalizacja użycia aplikacji. Mogą również chcieć połączyć różne aplikacje lub określone procedury logowania.

Aby osiągnąć ten cel, musimy określić identyfikatory sesji i czas opóźnienia między ostatnią sesją, która wystąpiła.

Musimy znaleźć poprzednie logowanie, a następnie przypisać to logowanie do wszystkich sesji, które są generowane do tej samej aplikacji. Pierwszym wyzwaniem jest to, że skrypt podstawowy U-SQL nie pozwala nam na stosowanie obliczeń na już obliczonych kolumnach z funkcją LGD. Drugim wyzwaniem jest to, że musimy zachować konkretną sesję dla wszystkich sesji w tym samym okresie.

Aby rozwiązać ten problem, używamy zmiennej globalnej `static public string globalSession;`w sekcji związanej z kodem: .

Ta zmienna globalna jest stosowana do całego zestawu wierszy podczas wykonywania skryptu.

Oto sekcja dotycząca kodu naszego programu U-SQL:

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

W tym przykładzie `static public string globalSession;` pokazano `getStampUserSession` zmienną globalną używaną wewnątrz funkcji i uzyskiwanie ponownego inicjalizowania za każdym razem, gdy parametr Session jest zmieniany.

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

Funkcja `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` jest wywoływana w tym miejscu podczas obliczania drugiego zestawu wierszy pamięci. Przekazuje kolumnę `UserSessionTimestamp` i zwraca `UserSessionTimestamp` wartość, dopóki nie ujmuje się.

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

W tym przykładzie pokazano bardziej skomplikowany scenariusz przypadku użycia, w którym używamy zmiennej globalnej wewnątrz sekcji związanej z kodem, która jest stosowana do całego zestawu wierszy pamięci.

## <a name="use-user-defined-types-udt"></a>Używanie typów zdefiniowanych przez użytkownika: UDT
Typy zdefiniowane przez użytkownika lub UDT to kolejna funkcja programowalności języka U-SQL. U-SQL UDT działa jak zwykły typ zdefiniowany przez użytkownika języka C#. C# jest silnie wpisany język, który umożliwia korzystanie z wbudowanych i niestandardowych typów zdefiniowanych przez użytkownika.

U-SQL nie może niejawnie serializować lub de-serializować dowolnych UDTs, gdy UDT jest przekazywana między wierzchołkami w zestawach wierszy. Oznacza to, że użytkownik musi podać jawne formater przy użyciu interfejsu IFormatter. Zapewnia to U-SQL z metody serializacji i de-serializacji dla UDT.

> [!NOTE]
> Wbudowane ekstraktory i wyjścia U-SQL nie mogą obecnie serializować ani de-serializować danych UDT do lub z plików nawet z zestawem IFormatter. Tak więc podczas pisania danych UDT do pliku z OUTPUT instrukcji lub odczytu go z ekstraktorem, należy przekazać go jako ciąg lub tablicy bajtów. Następnie należy wywołać kod serializacji i deserializacji (czyli metoda ToString() UDT) jawnie. Z drugiej strony, zdefiniowane przez użytkownika ekstraktory i wyjścia mogą odczytywać i zapisywać UDTs.

Jeśli spróbujemy użyć UDT w EXTRACTOR lub OUTPUTTER (z poprzedniego SELECT), jak pokazano poniżej:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Otrzymujemy następujący błąd:

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

Aby pracować z UDT w outputter, musimy albo serializować go do ciągu z ToString() metody lub utworzyć niestandardowy outputter.

Obecnie nie można używać UDTs w grupie WEDŁUG. Jeśli UDT jest używany w GROUP BY, zgłaszany jest następujący błąd:

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

* Dodaj następujące obszary nazw:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Dodaj `Microsoft.Analytics.Interfaces`, co jest wymagane dla interfejsów UDT. Ponadto `System.IO` może być konieczne zdefiniowanie interfejsu IFormatter.

* Zdefiniuj typ zdefiniowany za pomocą atrybutu SqlUserDe defineedType.

**SqlUserDefinedType** służy do oznaczania definicji typu w zestawie jako typu zdefiniowanego przez użytkownika (UDT) w U-SQL. Właściwości atrybutu odzwierciedlają fizyczne właściwości UDT. Klasa ta nie może być dziedziczona.

SqlUserDefinedType jest atrybutem wymaganym dla definicji UDT.

Konstruktor klasy:  

* SqlUserDefinedTypeAttribute (formater typów)

* Formater typu: Wymagany parametr do zdefiniowania formatera UDT - `IFormatter` w szczególności typ interfejsu musi być przekazany w tym miejscu.

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

The `IFormatter` interface serializes and de-serializes an object graph with the root type of \<typeparamref name="T">.

\<typeparam name="T">Typ główny wykresu obiektu do serializacji i de-serializacji.

* **Deserialize:** De-serializes danych na dostarczonym strumieniu i rekonstytuuje wykres obiektów.

* **Serialize:** Serializes obiektu lub wykres obiektów, z danym katalogu głównego do dostarczonego strumienia.

`MyType`wystąpienie: Wystąpienie typu.  
`IColumnWriter`moduł `IColumnReader` zapisujący/czytnik: strumień kolumny źródłowej.  
`ISerializationContext`kontekst: Wyliczenie, który definiuje zestaw flag, który określa kontekst źródłowy lub docelowy dla strumienia podczas serializacji.

* **Pośredni:** Określa, że kontekst źródłowy lub docelowy nie jest magazynem utrwalonych.

* **Trwałość:** Określa, że kontekst źródłowy lub docelowy jest magazynem utrwalone.

Jako zwykły typ języka C# definicja U-SQL UDT może zawierać zastąpienia dla operatorów, takich jak +/==/!=. Może również zawierać metody statyczne. Na przykład jeśli mamy zamiar użyć tego UDT jako parametr u-SQL MIN funkcji agregacji, musimy zdefiniować < zastępowanie operatora.

Wcześniej w tym przewodniku, wykazaliśmy przykład identyfikacji okresu obrachunkowego od konkretnej daty w formacie `Qn:Pn (Q1:P10)`. W poniższym przykładzie pokazano, jak zdefiniować typ niestandardowy dla wartości okresu obrachunkowego.

Poniżej przedstawiono przykład sekcji bez kodu z niestandardowym interfejsem UDT i IFormatter:

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

Zdefiniowany typ obejmuje dwie liczby: kwartał i miesiąc. Operatory `==/!=/>/<` i `ToString()` metoda statyczna są zdefiniowane w tym miejscu.

Jak wspomniano wcześniej, UDT może być używany w wyrażeniach SELECT, ale nie może być używany w OUTPUTTER/EXTRACTOR bez niestandardowej serializacji. Albo musi być serializowany jako `ToString()` ciąg z lub używane z niestandardowych OUTPUTTER/EXTRACTOR.

Teraz porozmawiajmy o użyciu UDT. W sekcji związanej z kodem zmieniliśmy naszą funkcję GetFiscalPeriod na następującą:

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

Jak widać, zwraca wartość naszego typu FiscalPeriod.

Poniżej przedstawiamy przykład, jak używać go dalej w skrypcie podstawowym U-SQL. W tym przykładzie przedstawiono różne formy wywołania UDT ze skryptu U-SQL.

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

Oto przykład pełnej sekcji dotyczącej kodu:

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

## <a name="use-user-defined-aggregates-udagg"></a>Używanie agregatów zdefiniowanych przez użytkownika: UDAGG
Agregaty zdefiniowane przez użytkownika to wszystkie funkcje związane z agregacją, które nie są dostarczane z u-SQL. Przykładem może być agregacja do wykonywania niestandardowych obliczeń matematycznych, konkadowania ciągów, manipulacje z ciągami i tak dalej.

Zdefiniowana przez użytkownika definicja klasy podstawowej agregacji jest następująca:

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

**SqlUserDefinedAggregate** wskazuje, że typ powinien być zarejestrowany jako agregacja zdefiniowana przez użytkownika. Klasa ta nie może być dziedziczona.

Atrybut SqlUserDedefedType jest **opcjonalny** dla definicji UDAGG.


Klasa podstawowa umożliwia przekazywanie trzech parametrów abstrakcyjnych: dwa jako parametry wejściowe i jeden jako wynik. Typy danych są zmienne i powinny być zdefiniowane podczas dziedziczenia klasy.

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

* **Init** wywołuje raz dla każdej grupy podczas obliczeń. Zapewnia procedurę inicjowania dla każdej grupy agregacji.  
* **Akumuluj** jest wykonywany raz dla każdej wartości. Zapewnia główne funkcje algorytmu agregacji. Może służyć do agregowania wartości z różnych typów danych, które są zdefiniowane podczas dziedziczenia klasy. Może akceptować dwa parametry typów danych zmiennych.
* **Terminate** jest wykonywany raz na grupę agregacji na końcu przetwarzania do wyprowadzenia wyniku dla każdej grupy.

Aby zadeklarować poprawne typy danych wejściowych i wyjściowych, należy użyć definicji klasy w następujący sposób:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Pierwszy parametr do akumulacji
* T2: Drugi parametr do akumulacji
* TResult: Zwraca typ zakończenia

Przykład:

```
public class GuidAggregate : IAggregate<string, int, int>
```

lub

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Użyj UDAGG w U-SQL
Aby użyć UDAGG, najpierw zdefiniuj go w kodzie lub odwołaj się do niego z istniejącej biblioteki DLL programowania, jak wspomniano wcześniej.

Następnie użyj następującej składni:

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

I podstawowy skrypt U-SQL:

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

W tym scenariuszu przypadku użycia łączymy identyfikatory GUID klasy dla określonych użytkowników.

## <a name="use-user-defined-objects-udo"></a>Używanie obiektów zdefiniowanych przez użytkownika: UDO
U-SQL umożliwia definiowanie niestandardowych obiektów programowalności, które są nazywane obiektami zdefiniowanymi przez użytkownika lub UDO.

Poniżej znajduje się lista UDO w U-SQL:

* Ekstraktory zdefiniowane przez użytkownika
    * Wyodrębnianie wiersza po wierszu
    * Służy do implementowania wyodrębniania danych z niestandardowych plików strukturalnych

* Dane wyjściowe zdefiniowane przez użytkownika
    * Wynik wiersz po wierszu
    * Służy do wyprowadzania niestandardowych typów danych lub niestandardowych formatów plików

* Procesory zdefiniowane przez użytkownika
    * Weź jeden wiersz i wyprodukuj jeden wiersz
    * Służy do zmniejszania liczby kolumn lub tworzenia nowych kolumn z wartościami pochodnymi istniejącego zestawu kolumn

* 2019 r.
    * Weź jeden wiersz i wyprodukuj od 0 do n wierszy
    * Używany z NA ZASTOSOWANIEM OUTER/CROSS

* Kombajny zdefiniowane przez użytkownika
    * Łączy zestawy wierszy — zdefiniowane przez użytkownika numery JOIN

* Reduktory zdefiniowane przez użytkownika
    * Weź n wiersze i wyprodukuj jeden wiersz
    * Służy do zmniejszania liczby wierszy

UDO jest zazwyczaj wywoływana jawnie w skrypcie U-SQL jako część następujących instrukcji U-SQL:

* EXTRACT
* Wyjście
* Proces
* Połączyć
* Zmniejszyć

> [!NOTE]  
> UDO są ograniczone do korzystania z pamięci 0.5Gb.  To ograniczenie pamięci nie ma zastosowania do lokalnych wykonań.

## <a name="use-user-defined-extractors"></a>Korzystanie z ekstraktorów zdefiniowanych przez użytkownika
U-SQL umożliwia importowanie danych zewnętrznych przy użyciu instrukcji EXTRACT. Instrukcja EXTRACT może używać wbudowanych ekstraktorów UDO:  

* *Extractors.Text()*: Zapewnia wyodrębnianie z rozdzielonych plików tekstowych o różnych kodowaniach.

* *Extractors.Csv()*: Umożliwia wyodrębnianie z plików wartości oddzielonych przecinkami (CSV) o różnych kodowaniach.

* *Extractors.Tsv()*: Umożliwia wyodrębnianie z plików wartości oddzielonych kartą (TSV) o różnych kodach.

Może być przydatne do opracowania niestandardowego ekstraktora. Może to być pomocne podczas importowania danych, jeśli chcemy wykonać dowolne z następujących zadań:

* Modyfikuj dane wejściowe, dzieląc kolumny i modyfikując poszczególne wartości. Funkcja procesora jest lepsza do łączenia kolumn.
* Analizuj nieustrukturyzowane dane, takie jak strony sieci Web i wiadomości e-mail, lub dane półstrukturalne, takie jak XML/JSON.
* Analizuj dane w nieobsługiwym kodowaniu.

Aby zdefiniować ekstraktor zdefiniowany przez użytkownika lub UDE, musimy utworzyć `IExtractor` interfejs. Wszystkie parametry wejściowe do ekstraktora, takie jak ograniczniki kolumn/wierszy i kodowanie, muszą być zdefiniowane w konstruktorze klasy. Interfejs `IExtractor` powinien również zawierać definicję zastąpienia w `IEnumerable<IRow>` następujący sposób:

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

**Atrybut SqlUserDedefdefedExtractor** wskazuje, że typ powinien być zarejestrowany jako ekstraktor zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

SqlUserDefinedExtractor jest opcjonalnym atrybutem definicji UDE. Służy do definiowania AtomicFileProcessing właściwości dla obiektu UDE.

* bool AtomicFileProcessing   

* **true** = Wskazuje, że ten ekstraktor wymaga atomowych plików wejściowych (JSON, XML, ...)
* **false** = Wskazuje, że ten ekstraktor może zajmować się podzielonymi / rozproszonymi plikami (CSV, SEQ, ...)

Głównymi obiektami programowalności UDE są **wejścia** i **wyjścia**. Obiekt wejściowy służy do wyliczania danych wejściowych jako `IUnstructuredReader`. Obiekt wyjściowy służy do ustawiania danych wyjściowych w wyniku działania ekstraktora.

Dane wejściowe są `System.IO.Stream` dostępne `System.IO.StreamReader`za pośrednictwem i .

W przypadku wyliczenia kolumn wejściowych najpierw dzielimy strumień wejściowy przy użyciu ogranicznika wiersza.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Następnie należy dodatkowo podzielić wiersz wejściowy na części kolumn.

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

Ważne jest, aby zrozumieć, że niestandardowy ekstraktor tylko wyprowadza kolumny i wartości, które są zdefiniowane z danych wyjściowych. Ustaw wywołanie metody.

```
output.Set<string>(count, part);
```

Rzeczywiste wyjście ekstraktora jest wyzwalane przez wywołanie `yield return output.AsReadOnly();`.

Oto przykład ekstraktora:

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

W tym scenariuszu przypadku użycia ekstraktor ponownie generuje identyfikator GUID dla kolumny "guid" i konwertuje wartości kolumny "użytkownik" na wielkie litery. Niestandardowe ekstraktory mogą dawać bardziej skomplikowane wyniki, analizując dane wejściowe i manipulując nimi.

Poniżej znajduje się podstawowy skrypt U-SQL, który używa niestandardowego ekstraktora:

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

## <a name="use-user-defined-outputters"></a>Używanie danych wyjściowych zdefiniowanych przez użytkownika
Dane wyjściowe zdefiniowane przez użytkownika to kolejne UDO U-SQL, które umożliwia rozszerzenie wbudowanej funkcji U-SQL. Podobnie jak ekstraktor, istnieje kilka wbudowanych outputters.

* *Outputters.Text()*: Zapisuje dane do rozdzielonych plików tekstowych o różnych kodowaniach.
* *Outputters.Csv()*: Zapisuje dane do plików csv (comma-separated) różnych kodowania.
* *Outputters.Tsv()*: Zapisuje dane do plików wartości oddzielonych kartą (TSV) o różnych kodowaniach.

Niestandardowy wyprowadzacz umożliwia zapisywanie danych w niestandardowym formacie zdefiniowanym. Może to być przydatne w przypadku następujących zadań:

* Zapisywanie danych do plików o częściowo ustrukturyzowanych lub nieustrukturyzowanych.
* Zapisywanie danych nie obsługiwanych kodowania.
* Modyfikowanie danych wyjściowych lub dodawanie atrybutów niestandardowych.

Aby zdefiniować dane wyjściowe zdefiniowane `IOutputter` przez użytkownika, musimy utworzyć interfejs.

Poniżej przedstawiono implementację klasy podstawowej: `IOutputter`

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Wszystkie parametry wejściowe do wyjścia, takie jak ograniczniki kolumn/wierszy, kodowanie i tak dalej, muszą być zdefiniowane w konstruktorze klasy. Interfejs `IOutputter` powinien również zawierać `void Output` definicję zastąpienia. Atrybut `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` można opcjonalnie ustawić dla przetwarzania plików niepodzielnie. Aby uzyskać więcej informacji, zobacz następujące szczegóły.

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

* `Output`jest wywoływana dla każdego wiersza wejściowego. Zwraca zestaw `IUnstructuredWriter output` wierszy.
* Klasa Konstruktora służy do przekazywania parametrów do pliku wyjściowego zdefiniowanego przez użytkownika.
* `Close`jest używany do opcjonalnie zastąpić do wydania stanu kosztowne lub określić, kiedy ostatni wiersz został napisany.

**Atrybut SqlUserDedefedOutputter** wskazuje, że typ powinien być zarejestrowany jako plik wyjściowy zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

SqlUserDefinedOutputter jest opcjonalnym atrybutem dla zdefiniowanej przez użytkownika definicji wyjścia. Służy do definiowania AtomicFileProcessing właściwości.

* bool AtomicFileProcessing   

* **true** = Wskazuje, że ten wyprowadzacz wymaga plików wyjściowych atomowych (JSON, XML, ...)
* **false** = Wskazuje, że ten wyprowadzacz może radzić sobie z podzielonymi / rozproszonymi plikami (CSV, SEQ, ...)

Głównymi obiektami programowalności są **wiersze** i **dane wyjściowe**. Obiekt **wiersza** służy do wyliczania danych wyjściowych jako `IRow` interfejsu. **Dane wyjściowe** służą do ustawiania danych wyjściowych do pliku docelowego.

Dane wyjściowe są dostępne `IRow` za pośrednictwem interfejsu. Dane wyjściowe są przekazywane wiersz w czasie.

Poszczególne wartości są wyliczana przez wywołanie Get metody interfejsu IRow:

```
row.Get<string>("column_name")
```

Poszczególne nazwy kolumn można `row.Schema`określić, dzwoniąc:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Takie podejście umożliwia tworzenie elastycznego modułu wyjściowego dla dowolnego schematu metadanych.

Dane wyjściowe są zapisywane `System.IO.StreamWriter`do pliku za pomocą programu . Parametr strumienia jest `output.BaseStream` ustawiony `IUnstructuredWriter output`jako część .

Należy zauważyć, że ważne jest opróżnienie buforu danych do pliku po każdej iteracji wiersza. Ponadto `StreamWriter` obiekt musi być używany z atrybutem Disposable (domyślnie) i ze słowem kluczowym **using:**

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

W przeciwnym razie wywołaj metodę Flush() jawnie po każdej iteracji. Pokazujemy to w poniższym przykładzie.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ustawianie nagłówków i stopek dla określonego przez użytkownika wyjścia
Aby ustawić nagłówek, należy użyć przepływu wykonywania pojedynczej iteracji.

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

Kod w pierwszym `if (isHeaderRow)` bloku jest wykonywany tylko raz.

W przypadku stopki należy użyć odwołania `System.IO.Stream` do`output.BaseStream`wystąpienia obiektu ( ). Zapisz stopka w Close() metody `IOutputter` interfejsu.  (Aby uzyskać więcej informacji, zobacz poniższy przykład).

Poniżej znajduje się przykład dane wyjściowe zdefiniowane przez użytkownika:

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

I skrypt podstawowy U-SQL:

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

Jest to wyprowadzacz HTML, który tworzy plik HTML z danymi tabeli.

### <a name="call-outputter-from-u-sql-base-script"></a>Wywoływanie wyprowadzania ze skryptu podstawowego U-SQL
Aby wywołać niestandardowy wyprowadzacz z podstawowego skryptu U-SQL, należy utworzyć nowe wystąpienie obiektu wyjściowego.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Aby uniknąć tworzenia wystąpienia obiektu w skrypcie podstawowym, możemy utworzyć otokę funkcji, jak pokazano w naszym wcześniejszym przykładzie:

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

W takim przypadku oryginalne wywołanie wygląda następująco:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Korzystanie z procesorów zdefiniowanych przez użytkownika
Procesor zdefiniowany przez użytkownika lub UDP jest typem UDO U-SQL, który umożliwia przetwarzanie przychodzących wierszy przez zastosowanie funkcji programowalności. UDP umożliwia łączenie kolumn, modyfikowanie wartości i dodawanie nowych kolumn, jeśli to konieczne. Zasadniczo pomaga przetwarzać zestaw wierszy do tworzenia elementów danych wymaganych.

Aby zdefiniować UDP, musimy `IProcessor` utworzyć interfejs `SqlUserDefinedProcessor` z atrybutem, który jest opcjonalny dla UDP.

Ten interfejs powinien zawierać `IRow` definicję zastępowania zestawu wierszy interfejsu, jak pokazano w poniższym przykładzie:

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

**SqlUserDefinedProcessor** wskazuje, że typ powinien być zarejestrowany jako procesor zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

Atrybut SqlUserDedefdefedProcessor jest **opcjonalny** dla definicji UDP.

Głównymi obiektami programowalności są **wejścia** i **wyjścia**. Obiekt wejściowy służy do wyliczania kolumn wejściowych i danych wyjściowych oraz do ustawiania danych wyjściowych w wyniku działania procesora.

W przypadku wyliczenia kolumn wejściowych używamy `input.Get` tej metody.

```
string column_name = input.Get<string>("column_name");
```

Parametr dla `input.Get` metody jest kolumna, która jest `PRODUCE` przekazywana jako część `PROCESS` klauzuli instrukcji skryptu podstawowego U-SQL. Musimy użyć poprawnego typu danych tutaj.

W przypadku danych `output.Set` wyjściowych należy użyć metody.

Należy pamiętać, że producent niestandardowy tylko wyprowadza kolumny i wartości, które są zdefiniowane za `output.Set` pomocą wywołania metody.

```
output.Set<string>("mycolumn", mycolumn);
```

Rzeczywiste dane wyjściowe procesora `return output.AsReadOnly();`są wyzwalane przez wywołanie .

Oto przykład procesora:

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

W tym scenariuszu przypadku użycia procesor generuje nową kolumnę o nazwie "full_description" przez połączenie istniejących kolumn — w tym przypadku "użytkownik" wielkimi literami i "des". Regeneruje również identyfikator GUID i zwraca oryginalne i nowe wartości guid.

Jak widać z poprzedniego przykładu, można wywołać `output.Set` metody Języka C# podczas wywoływania metody.

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

## <a name="use-user-defined-appliers"></a>Korzystanie z ceł ceł ceł zdefiniowanych przez użytkownika
Pewien U-SQL użytkownik zdefiniowany rozczyanek umożliwia wywołanie pewien zwyczaj C# funkcja pod kątem każdy wiersz który' zwracany przy ten zewnętrzny tabela wyrażenie od pewien zapytanie. Prawe dane wejściowe są oceniane dla każdego wiersza z lewego wejścia, a wiersze, które są produkowane są łączone dla danych wyjściowych końcowych. Lista kolumn, które są produkowane przez APPLY operatora są kombinacją zestawu kolumn w lewej i prawej danych wejściowych.

20.00.

Typowe wywołanie do zastosowania zdefiniowanego przez użytkownika wygląda następująco:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Aby uzyskać więcej informacji na temat używania cedów w wyrażeniu SELECT, zobacz [U-SQL SELECT Wybieranie z ZASTOSOWANIA KRZYŻOWEGO i ZASTOSUJ ZEWNĘTRZNIE](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Definicja klasy podstawowej podstawowego nika wiązanika zdefiniowanego przez użytkownika jest następująca:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Aby zdefiniować wiązkę zastosowania zdefiniowaną `IApplier` przez użytkownika, musimy utworzyć interfejs z atrybutem [`SqlUserDefinedApplier`], który jest opcjonalny dla zdefiniowanej przez użytkownika definicji wiązki definicji.

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

* Zastosuj jest wywoływana dla każdego wiersza tabeli zewnętrznej. Zwraca zestaw `IUpdatableRow` wierszy wyjściowych.
* Klasa Konstruktora służy do przekazywania parametrów do stosowane zdefiniowanego przez użytkownika.

**SqlUserDefinedApplier** wskazuje, że typ powinien być zarejestrowany jako likwal definiowany przez użytkownika. Klasa ta nie może być dziedziczona.

**SqlUserDefinedApplier** jest **opcjonalny** dla zdefiniowanej przez użytkownika definicji likdatora.


Główne obiekty programowalności są następujące:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Zestawy wierszy wejściowych `IRow` są przekazywane jako dane wejściowe. Wiersze danych wyjściowych `IUpdatableRow` są generowane jako interfejs wyjściowy.

Poszczególne nazwy kolumn można określić, wywołując `IRow` Schema metody.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Aby uzyskać rzeczywiste wartości danych `IRow`z przychodzącego , `IRow` używamy Get() metoda interfejsu.

```
mycolumn = row.Get<int>("mycolumn")
```

Lub używamy nazwy kolumny schematu:

```
row.Get<int>(row.Schema[0].Name)
```

Wartości wyjściowe muszą `IUpdatableRow` być ustawione z wyjściem:

```
output.Set<int>("mycolumn", mycolumn)
```

Ważne jest, aby zrozumieć, że niestandardowe appliers tylko `output.Set` kolumny wyjściowe i wartości, które są zdefiniowane za pomocą wywołania metody.

Rzeczywiste dane wyjściowe są `yield return output.AsReadOnly();`wyzwalane przez wywołanie .

Parametry dyszce zdefiniowane przez użytkownika mogą być przekazywane do konstruktora. Applier może zwrócić zmienną liczbę kolumn, które muszą być zdefiniowane podczas wywołania 201/07 w podstawowym skrypcie U-SQL.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Oto przykład nika 2013/

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

Poniżej znajduje się podstawowy skrypt U-SQL dla tego zastosowania zdefiniowanego przez użytkownika:

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

W tym przypadku użycia dezyl ów działa jako parser wartości rozdzielanych przecinkami dla właściwości floty samochodowej. Wiersze pliku wejściowego wyglądają następująco:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Jest to typowy plik TSV rozdzielany kartą z kolumną właściwości, która zawiera właściwości samochodu, takie jak make i model. Te właściwości muszą być analizowane do kolumn tabeli. Obiekt, który jest dostarczany umożliwia również generowanie dynamicznej liczby właściwości w zestawie wierszy wyników, na podstawie parametru, który jest przekazywany. Można wygenerować wszystkie właściwości lub tylko określony zestaw właściwości.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

20.00 2019 r. można wywołać jako nowe wystąpienie obiektu 2010:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Lub z wywołaniem metody fabrycznej otoki:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Korzystanie z kombajników zdefiniowanych przez użytkownika
Kombajn zdefiniowany przez użytkownika lub UDC umożliwia łączenie wierszy z lewej i prawej zestawy wierszy, w oparciu o logikę niestandardową. Kombajn zdefiniowany przez użytkownika jest używany z wyrażeniem COMBINE.

Kombajn jest wywoływany z wyrażeniem COMBINE, które zawiera niezbędne informacje o zestawach wierszy wejściowych, kolumnach grupowania, schematie oczekiwanego wyniku i dodatkowych informacjach.

Aby wywołać kombajn w podstawowym skrypcie U-SQL, używamy następującej składni:

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

Aby uzyskać więcej informacji, zobacz [ŁĄCZENIE wyrażenia (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Aby zdefiniować kombajnia zdefiniowane przez `ICombiner` użytkownika,`SqlUserDefinedCombiner`musimy utworzyć interfejs z atrybutem [ ], który jest opcjonalny dla zdefiniowanej przez użytkownika definicji kombajny.

Definicja klasy podstawowej: `ICombiner`

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

Niestandardowa implementacja `ICombiner` interfejsu powinna zawierać `IEnumerable<IRow>` definicję zastąpienia łączyć.

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

**Atrybut SqlUserDefinedCombiner** wskazuje, że typ powinien być zarejestrowany jako kombajniara zdefiniowana przez użytkownika. Klasa ta nie może być dziedziczona.

**SqlUserDefinedCombiner** służy do definiowania właściwości trybu kombajny. Jest to opcjonalny atrybut dla zdefiniowanej przez użytkownika definicji kombajnika.

Tryb trybu kombinatora

Wyliczenia CombinerMode może przyjmować następujące wartości:

* Pełna (0) Każdy wiersz wyjściowy potencjalnie zależy od wszystkich wierszy wejściowych z lewej i prawej strony o tej samej wartości klucza.

* Po lewej (1) Każdy wiersz wyjściowy zależy od pojedynczego wiersza wejściowego od lewej (i potencjalnie wszystkich wierszy z prawej strony o tej samej wartości klucza).

* Po prawej (2) Każdy wiersz wyjściowy zależy od pojedynczego wiersza wejściowego od prawej (i potencjalnie wszystkich wierszy z lewej strony o tej samej wartości klucza).

* Wewnętrzny (3) Każdy wiersz wyjściowy zależy od pojedynczego wiersza wejściowego od lewej i prawej strony o tej samej wartości.

Przykład:`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`[ ]


Głównymi obiektami programowalności są:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Zestawy wierszy wejściowych są przekazywane jako **lewy** i **prawy** `IRowset` typ interfejsu. Oba zestawy wierszy muszą być wyliczone do przetwarzania. Można wyliczyć każdy interfejs tylko raz, więc musimy wyliczyć i buforować go w razie potrzeby.

Do celów buforowania możemy utworzyć listę\<\> T typu struktury pamięci w wyniku wykonywania kwerendy LINQ, `IRow` w szczególności lista<>. Typ danych anonimowych może być również używany podczas wyliczania.

Zobacz [Wprowadzenie do zapytań LINQ (C#), aby](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) uzyskać więcej informacji na temat zapytań LINQ i\< [IEnumerable\<T\> Interface,](/dotnet/api/system.collections.generic.ienumerable-1) aby uzyskać więcej informacji na temat interfejsu IEnumerable T.\>

Aby uzyskać rzeczywiste wartości danych `IRowset`z przychodzącego , `IRow` używamy Get() metoda interfejsu.

```
mycolumn = row.Get<int>("mycolumn")
```

Poszczególne nazwy kolumn można określić, wywołując `IRow` Schema metody.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Lub przy użyciu nazwy kolumny schematu:

```
c# row.Get<int>(row.Schema[0].Name)
```

Ogólne wyliczenie z LINQ wygląda następująco:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Po wyliczenie obu zestawów wierszy, mamy zamiar pętli przez wszystkie wiersze. Dla każdego wiersza w lewym zestawie wierszy znajdziemy wszystkie wiersze, które spełniają stan naszego kombajny.

Wartości wyjściowe muszą `IUpdatableRow` być ustawione z wyjściem.

```
output.Set<int>("mycolumn", mycolumn)
```

Rzeczywiste dane wyjściowe są `yield return output.AsReadOnly();`wyzwalane przez wywołanie do .

Oto przykład kombajny:

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

W tym scenariuszu użycia budujemy raport analityczny dla sprzedawcy detalicznego. Celem jest znalezienie wszystkich produktów, które kosztują więcej niż $20,000 i które sprzedają się za pośrednictwem strony internetowej szybciej niż za pośrednictwem zwykłego sprzedawcy detalicznego w określonym czasie.

Oto podstawowy skrypt U-SQL. Można porównać logikę między zwykłym SPRZĘŻENIE i kombajnem:

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

Kombajnia zdefiniowana przez użytkownika może być wywoływana jako nowe wystąpienie obiektu edycyjnego:

```
USING new MyNameSpace.MyCombiner();
```


Lub z wywołaniem metody fabrycznej otoki:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Korzystanie z reduktorów zdefiniowanych przez użytkownika

U-SQL umożliwia pisanie niestandardowych reduktorów zestawu wierszy w języku C# przy użyciu struktury rozszerzalności operatora zdefiniowanego przez użytkownika i implementowania interfejsu IReducer.

Zdefiniowany przez użytkownika reduktor lub UDR może służyć do eliminowania niepotrzebnych wierszy podczas wyodrębniania (importowania). Może również służyć do manipulowania i oceny wierszy i kolumn. Na podstawie logiki programowania można również zdefiniować, które wiersze muszą zostać wyodrębnione.

Aby zdefiniować klasę UDR, musimy `IReducer` utworzyć interfejs `SqlUserDefinedReducer` z atrybutem opcjonalnym.

Ten interfejs klasy powinien zawierać `IEnumerable` definicję zastępowania zestawu wierszy interfejsu.

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

**Atrybut SqlUserDefinedReducer** wskazuje, że typ powinien być zarejestrowany jako reduktor zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.
**SqlUserDefinedReducer** jest opcjonalnym atrybutem dla zdefiniowanej przez użytkownika definicji reduktora. Służy do definiowania Właściwości IsRecursive.

* bool isrecursive bool isrecursive bool isrecursive bool    
* **true** = wskazuje, czy reduktor jest asocyjny i przemienny

Głównymi obiektami programowalności są **wejścia** i **wyjścia**. Obiekt wejściowy służy do wyliczania wierszy wejściowych. Dane wyjściowe są używane do ustawiania wierszy danych wyjściowych w wyniku zmniejszenia aktywności.

W przypadku wyliczenia wierszy `Row.Get` wejściowych używamy tej metody.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametr dla `Row.Get` metody jest kolumna, która jest `PRODUCE` przekazywana `REDUCE` jako część klasy instrukcji skryptu podstawowego U-SQL. Musimy również użyć poprawnego typu danych.

W przypadku danych `output.Set` wyjściowych należy użyć metody.

Ważne jest, aby zrozumieć, że niestandardowy reduktor `output.Set` tylko wyprowadza wartości, które są zdefiniowane za pomocą wywołania metody.

```
output.Set<string>("mycolumn", guid);
```

Rzeczywiste wyjście reduktora jest `yield return output.AsReadOnly();`wyzwalane przez wywołanie .

Poniżej znajduje się przykład reduktora:

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

W tym scenariuszu przypadku użycia reduktor pomija wiersze z pustą nazwą użytkownika. Dla każdego wiersza w zestawie wierszy odczytuje każdą wymaganą kolumnę, a następnie ocenia długość nazwy użytkownika. Wyprowadza rzeczywisty wiersz tylko wtedy, gdy długość wartości nazwy użytkownika jest większa niż 0.

Poniżej znajduje się podstawowy skrypt U-SQL, który używa niestandardowego reduktora:

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
