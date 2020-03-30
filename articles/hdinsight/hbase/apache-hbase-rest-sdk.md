---
title: Korzystanie z dysku SDK platformy .NET bazy danych HBase — Usługa Azure HDInsight
description: Użyj HBase .NET SDK do tworzenia i usuwania tabel oraz do odczytu i zapisu danych.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: eba7d7ad009b2ef0442a916983489489eb5cceb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806664"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Użyj pliku .NET SDK dla bazy danych Apache HBase

[Apache HBase](apache-hbase-overview.md) oferuje dwie podstawowe możliwości pracy z danymi: [zapytania Apache Hive i wywołania interfejsu API RESTful firmy HBase.](apache-hbase-tutorial-get-started-linux.md) Interfejs API REST można pracować bezpośrednio `curl` za pomocą polecenia lub podobnego narzędzia.

W przypadku aplikacji Języka C# i .NET [biblioteka klienta REST firmy Microsoft HBase dla platformy .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) udostępnia bibliotekę klienta na interfejsie API HBase REST.

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

Pakiet HBase .NET SDK jest dostarczany jako pakiet NuGet, który można zainstalować z **konsoli Menedżera pakietów Programu** Visual Studio NuGet za pomocą następującego polecenia:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Tworzenie wystąpienia nowego obiektu HBaseClient

Aby użyć zestawu SDK, wystąpienia `HBaseClient` nowego obiektu, przekazywania w `ClusterCredentials` składa się z `Uri` do klastra i Hadoop nazwę użytkownika i hasło.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Zastąp clustername nazwą klastra HDInsight HBase, a nazwę użytkownika i hasło poświadczeniami Apache Hadoop określonymi podczas tworzenia klastra. Domyślną nazwą użytkownika Hadoop jest **admin**.

## <a name="create-a-new-table"></a>Tworzenie nowej tabeli

HBase przechowuje dane w tabelach. Tabela składa się z *klawisza wiersza*, klucza podstawowego i jednej lub więcej grup kolumn nazywanych *rodzinami kolumn*. Dane w każdej tabeli są rozkładane poziomo przez zakres klawiszy wiersza w *regionach*. Każdy region ma klucz początkowy i końcowy. Tabela może mieć jeden lub więcej regionów. Wraz z rozwojem danych w tabeli HBase dzieli duże regiony na mniejsze regiony. Regiony są przechowywane na *serwerach regionów,* gdzie jeden serwer regionu może przechowywać wiele regionów.

Dane są fizycznie przechowywane w *plikach HFiles*. Pojedynczy plik H Zawiera dane dla jednej tabeli, jednego regionu i jednej rodziny kolumn. Wiersze w pliku HFile są przechowywane posortowane na klawisz rowu. Każdy HFile ma *indeks drzewa B +* do szybkiego pobierania wierszy.

Aby utworzyć nową tabelę, `TableSchema` określ a i kolumny. Poniższy kod sprawdza, czy tabela "RestSDKTable" już istnieje — jeśli nie, tabela jest tworzona.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Ta nowa tabela zawiera rodziny dwukolumnowe, t1 i t2. Ponieważ rodziny kolumn są przechowywane oddzielnie w różnych plikach HFiles, warto mieć oddzielną rodzinę kolumn dla często wyszukiwanych danych. W poniższym przykładzie [Wstaw dane](#insert-data) kolumny są dodawane do rodziny kolumn t1.

## <a name="delete-a-table"></a>Usuwanie tabeli

Aby usunąć tabelę:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Wstawianie danych

Aby wstawić dane, należy określić unikatowy klucz wiersza jako identyfikator wiersza. Wszystkie dane są `byte[]` przechowywane w tablicy. Poniższy kod definiuje i `title` `director`dodaje `release_date` , i kolumny do rodziny kolumn t1, ponieważ te kolumny są najczęściej dostępne. Kolumny `description` `tagline` i kolumny są dodawane do rodziny kolumn t2. W razie potrzeby można podzielić dane na rodziny kolumn.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase implementuje [Cloud BigTable](https://cloud.google.com/bigtable/), więc format danych wygląda następująco:

![Dane wyjściowe apache HBase](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Wybieranie danych

Aby odczytać dane z tabeli HBase, przekaż nazwę `GetCellsAsync` tabeli `CellSet`i klucz wiersza do metody, aby zwrócić plik .

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

W takim przypadku kod zwraca tylko pierwszy pasujący wiersz, ponieważ powinien istnieć tylko jeden wiersz dla unikatowego klucza. Zwracana wartość jest `string` zmieniana `byte[]` na format z tablicy. Można również przekonwertować wartość na inne typy, takie jak liczba całkowita daty premiery filmu:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Skanowanie nad wierszami

HBase używa `scan` do pobierania jednego lub więcej wierszy. W tym przykładzie żąda wielu wierszy w partiach 10 i pobiera dane, których wartości klucza są między 25 i 35. Po pobraniu wszystkich wierszy usuń skaner, aby wyczyścić zasoby.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z przykładem bazy danych Apache HBase w usłudze HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Tworzenie kompleksowej aplikacji dzięki [analizie nastrojów na Twitterze w czasie rzeczywistym dzięki Apache HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
