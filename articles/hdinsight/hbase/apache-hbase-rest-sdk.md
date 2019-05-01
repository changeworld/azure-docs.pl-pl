---
title: Użyj zestawu HBase .NET SDK — usługa Azure HDInsight
description: Umożliwia zestawu HBase .NET SDK do tworzenia i usuwania tabel oraz odczytywanie i zapisywanie danych.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: 707869880c5df619def2d707264b59e22e03c521
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720302"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Używanie zestawu SDK .NET dla bazy danych Apache HBase

[Apache HBase](apache-hbase-overview.md) oferuje dwie opcje podstawowego do pracy z danymi: [Zapytania usługi Apache Hive i wywołania interfejsu API RESTful bazy danych HBase w](apache-hbase-tutorial-get-started-linux.md). Można pracować bezpośrednio za pomocą interfejsu API REST `curl` polecenia lub podobnej użyteczności.

W przypadku aplikacji C# i .NET [biblioteki klienta REST HBase firmy Microsoft dla platformy .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) udostępnia bibliotekę klienta ponad interfejsem API REST HBase.

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

Zestawu HBase .NET SDK jest dostępna jako pakiet NuGet, które mogą być instalowane z Visual Studio **Konsola Menedżera pakietów NuGet** za pomocą następującego polecenia:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Utwórz wystąpienie nowego obiektu HBaseClient

Aby korzystanie z zestawu SDK, utworzyć `HBaseClient` obiektu, przekazując `ClusterCredentials` składa się z `Uri` klastra, nazwę użytkownika Hadoop i hasło.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Zamień CLUSTERNAME nazwy klastra HDInsight HBase i nazwy użytkownika i HASŁA przy użyciu poświadczeń usługi Apache Hadoop, określone w procesie tworzenia klastra. Domyślna nazwa użytkownika usługi Hadoop jest **administratora**.

## <a name="create-a-new-table"></a>Utwórz nową tabelę

Baza danych HBase przechowuje dane w tabelach. Tabela składa się z *Rowkey*, klucz podstawowy i co najmniej jedną grupę kolumn o nazwie *rodziny kolumn*. Dane w każdej tabeli w poziomie są rozmieszczane w zakresie Rowkey do *regionów*. Każdy region ma klucz rozpoczęcia i zakończenia. Tabela może mieć co najmniej jeden region. Wraz z rozwojem dane w tabeli HBase dzieli dużych regionów na mniejsze regionów. Regiony są przechowywane w *serwery regionów*, gdy jeden serwer regionu można przechowywać wiele regionów.

Dane fizycznie znajduje się w *HFiles*. Pojedynczy hFile — zawiera dane dla jednej tabeli, regionami i jednej rodziny kolumn. Wierszy w HFile przechowywane są sortowane według Rowkey. Została każda HFile *drzewie B* indeksu do szybkiego pobierania wierszy.

Aby utworzyć nową tabelę, określ `TableSchema` i kolumn. Poniższy kod sprawdza, czy utworzono tabelę, którą "RestSDKTable" już istnieje — w przeciwnym razie tabeli.

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

Ta nowa tabela ma dwie kolumny rodziny, t1 i t2. Ponieważ rodziny kolumn są oddzielnie przechowywane w różnych HFiles, dobrym pomysłem będzie mieć rodzina osobna kolumna danych często poszukiwanych. W następującym [wstawiania danych](#insert-data) przykład kolumny są dodawane do rodziny kolumn t1.

## <a name="delete-a-table"></a>Usuwanie tabeli

Aby usunąć tabelę:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Wstawianie danych

Wstawianie danych, Ustaw klucz wiersza Unikatowy identyfikator wiersza. Wszystkie dane są przechowywane w `byte[]` tablicy. Poniższy kod definiuje i dodaje `title`, `director`, i `release_date` kolumny do rodziny kolumn t1, jak te kolumny są najczęściej używanych. `description` i `tagline` kolumny są dodawane do rodziny kolumn t2. Dane można podzielić na rodziny kolumn, zgodnie z potrzebami.

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

Implementuje HBase [BigTable chmury](https://cloud.google.com/bigtable/), dlatego format danych wygląda podobnie do następującej:

![Użytkownik mający rolę użytkownika klastra](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>Wybieranie danych

Do odczytywania danych z tabeli bazy danych HBase, polega na przekazaniu klucza nazwy i wierszu tabeli do `GetCellsAsync` metodę, aby zwrócić `CellSet`.

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

W tym przypadku ten kod zwraca tylko pierwszy zgodnego wiersza, powinien istnieć tylko jeden wiersz dla Unikatowy klucz. Zwrócona wartość zostanie zmieniona na `string` formatowania z `byte[]` tablicy. Można również przeprowadzić konwersję wartości do innych typów, np. liczbę całkowitą dla filmu, Data wydania:

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

## <a name="scan-over-rows"></a>Skanowanie względem wierszy

Korzysta z bazy danych HBase `scan` do pobierania co najmniej jeden wiersz. W tym przykładzie żądania wiele wierszy w partii 10 i pobiera dane, których wartości klucza są od 25 do 35. Po pobraniu wszystkich wierszy, należy usunąć skanera, aby wyczyścić zasoby.

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

## <a name="next-steps"></a>Kolejne kroki

* [Rozpoczynanie pracy z przykładem bazy danych Apache HBase w HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Tworzenie aplikacji end-to-end z [analizowanie opinii na Twitterze w czasie rzeczywistym z bazy danych Apache HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
