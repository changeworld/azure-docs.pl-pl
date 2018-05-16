---
title: Korzystanie z zestawu SDK .NET HBase - Azure HDInsight | Dokumentacja firmy Microsoft
description: Korzystanie z zestawu SDK .NET HBase, tworzenia i usuwania tabel, jak i do odczytywania i zapisywania danych.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: f0e2c6412a965c73b0055a24c799e05ad582a8c7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="use-the-hbase-net-sdk"></a>Korzystanie z zestawu HBase .NET SDK

[HBase](apache-hbase-overview.md) udostępnia dwie opcje podstawowego do pracy z danymi: [gałąź rejestru, kwerendy i wywołania interfejsu API RESTful HBase w](apache-hbase-tutorial-get-started-linux.md). Można pracować bezpośrednio za pomocą interfejsu API REST `curl` polecenia lub podobne narzędzia.

W przypadku aplikacji C# i .NET [biblioteki klienta REST HBase firmy Microsoft dla programu .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) udostępnia bibliotekę klienta ponad interfejsem API REST HBase.

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

Zestaw .NET SDK HBase jest dostarczane jako pakietu NuGet, które można zainstalować z programu Visual Studio **Konsola Menedżera pakietów NuGet** przy użyciu następującego polecenia:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Uruchamianie nowego obiektu HBaseClient

Korzystanie z zestawu SDK, utworzyć obiekt klasy `HBaseClient` obiektu, przekazując `ClusterCredentials` składa się z `Uri` do klastra oraz nazwę użytkownika Hadoop i hasło.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Zamień NAZWAKLASTRA z nazwą klastra usługi HDInsight, HBase oraz nazwę użytkownika i hasło podane poświadczenia usługi Hadoop na utworzenie klastra. Domyślna nazwa użytkownika Hadoop jest **admin**.

## <a name="create-a-new-table"></a>Utwórz nową tabelę

HBase przechowuje dane w tabelach. Tabela składa się z *Rowkey*, klucz podstawowy i co najmniej jedną grupę kolumn o nazwie *rodzinami kolumn*. Danych w każdej tabeli w poziomie jest dystrybuowana do zakresu Rowkey *regionów*. Każdy region ma klucz rozpoczęcia i zakończenia. Tabela może mieć jeden lub więcej regionów. Wraz z rozwojem dane w tabeli, HBase dzieli dużych regionów na mniejsze części. Regiony są przechowywane w *serwerów region*, w której jeden serwer region można przechowywać wielu regionach.

Dane są fizycznie przechowywane w *HFiles*. Pojedynczy HFile zawiera dane dla jednej tabeli, jeden region i jednej rodziny kolumn. Wierszy w HFile przechowywane są sortowane według Rowkey. Każdy HFile ma *drzewie B* indeksu szybkiego pobierania wierszy.

Aby utworzyć nową tabelę, określ `TableSchema` i kolumn. Poniższy kod sprawdza, czy tabelę, która już istnieje "RestSDKTable" — w przeciwnym razie tabela została utworzona.

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

Ta nowa tabela ma dwie kolumny rodzin, t1 a t2. Ponieważ w różnych HFiles rodzinami kolumn są przechowywane oddzielnie, warto mieć rodziny często Współużytkuj dane z osobna kolumna. W następujących [wstawiania danych](#insert-data) przykład kolumny są dodawane do rodziny kolumn t1.

## <a name="delete-a-table"></a>Usuwanie tabeli

Aby usunąć tabeli:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Wstawianie danych

Wstawiania danych, Ustaw klucz wiersza Unikatowy identyfikator wiersza. Wszystkie dane są przechowywane w `byte[]` tablicy. Poniższy kod definiuje i dodaje `title`, `director`, i `release_date` kolumny do rodziny kolumn t1, jak te kolumny są najczęściej używane. `description` i `tagline` kolumny są dodawane do t2 rodziny kolumn. Dane można podzielić na rodziny kolumn w razie potrzeby.

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

HBase implementuje BigTable, więc format danych wygląda następująco:

![Użytkownik z rolą użytkownika klastra](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>Wybieranie danych

Odczytywanie danych z tabeli HBase, Przekaż tabeli nazwy i wiersz klucza do `GetCellsAsync` metodę, aby zwrócić `CellSet`.

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

W takim przypadku ten kod zwraca tylko pierwszego dopasowania wiersza, powinien istnieć tylko jeden wiersz Unikatowy klucz. Zwrócona wartość zostanie zmieniona w `string` formacie z `byte[]` tablicy. Możesz również przekonwertować wartości do innych typów, takich jak całkowitą dla filmu Data wydania:

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

## <a name="scan-over-rows"></a>Skanuj za pośrednictwem wierszy

Używa bazy danych HBase `scan` można pobrać co najmniej jeden wiersz. W tym przykładzie żądań wielu wierszy w partii 10 i pobiera dane, których wartości klucza są od 25 do 35. Po pobraniu wszystkich wierszy, Usuń skanera, aby wyczyścić zasoby.

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

* [Rozpoczynanie pracy z przykładem bazy danych Apache HBase w usłudze HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Tworzenie aplikacji na trasie o [analizowanie w czasie rzeczywistym usługi Twitter wskaźniki nastrojów klientów z bazy danych HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
