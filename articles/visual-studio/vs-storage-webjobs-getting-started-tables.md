---
title: Wprowadzenie do magazynu platformy Azure przy użyciu programu Visual Studio (projekty webjob)
description: Jak rozpocząć korzystanie z magazynu tabel platformy Azure w projekcie azure WebJobs w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu połączonych usług programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e4d8299c06bfa5b0f33bff8fa592a2fa549c695c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707616"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Wprowadzenie do usługi Azure Storage (projekty azure webjob)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Omówienie
Ten artykuł zawiera przykłady kodu języka C#, które pokazują, jak używać narzędzia Azure WebJobs SDK w wersji 1.x z usługą magazynu tabel platformy Azure. Przykłady kodu używają [zestawów SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) w wersji 1.x.

Usługa magazynu tabel platformy Azure umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.  Aby uzyskać więcej [informacji, zobacz Wprowadzenie do usługi Azure Table Storage przy użyciu platformy .NET.](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table)

Niektóre fragmenty kodu pokazują **table** atrybut używany w funkcjach, które są wywoływane ręcznie, to znaczy, nie przy użyciu jednego z atrybutów wyzwalacza.

## <a name="how-to-add-entities-to-a-table"></a>Jak dodać encje do tabeli

Aby dodać jednostki do tabeli, należy użyć **atrybutu Tabela** z parametrem **\<ICollector T>** lub **IAsyncCollector\<T>,** w którym **T** określa schemat jednostek, które chcesz dodać. Konstruktor atrybutów przyjmuje parametr string, który określa nazwę tabeli.

Poniższy przykładowy kod dodaje **jednostki osoby** do tabeli o nazwie *Ruch przychodzący*.

```csharp
[NoAutomaticTrigger]
public static void IngressDemo(
    [Table("Ingress")] ICollector<Person> tableBinding)
{
    for (int i = 0; i < 100000; i++)
    {
        tableBinding.Add(
            new Person() {
                PartitionKey = "Test",
                RowKey = i.ToString(),
                Name = "Name" }
            );
    }
}
```

Zazwyczaj typ używany z **ICollector** pochodzi z **TableEntity** lub implementuje **ITableEntity**, ale nie musi. Jedna z następujących **person** klasy pracy z kodem pokazanym w poprzedniej metody **transferu ruchu przychodzącego.**

```csharp
public class Person : TableEntity
{
    public string Name { get; set; }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Jeśli chcesz pracować bezpośrednio z interfejsem API magazynu platformy Azure, możesz dodać parametr **CloudStorageAccount** do podpisu metody.

## <a name="real-time-monitoring"></a>Monitorowanie w czasie rzeczywistym

Ponieważ funkcje transferu danych często przetwarzają duże ilości danych, pulpit nawigacyjny SDK WebJobs udostępnia dane monitorowania w czasie rzeczywistym. **Sekcja Dziennik wywołania** informuje, czy funkcja jest nadal uruchomiona.

![Uruchomienie funkcji transferu ruchu przychodzącego](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

**Strona Szczegóły wywołania** raportuje postęp funkcji (liczba napisanych jednostek) podczas jej działania i daje możliwość jej przerwania.

![Uruchomienie funkcji transferu ruchu przychodzącego](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Po zakończeniu funkcji strona **Szczegóły wywołania** raportuje liczbę zapisanych wierszy.

![Zakończono funkcję przynikania](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Jak odczytać wiele encji z tabeli

Aby odczytać tabelę, należy użyć **atrybutu Tabela** z parametrem **\<IQueryable T>,** w którym typ **T** pochodzi od **tableentity** lub implementuje **ITableEntity**.

Poniższy przykładowy kod odczytuje i rejestruje wszystkie wiersze z tabeli **przychodzących:**

```csharp
public static void ReadTable(
    [Table("Ingress")] IQueryable<Person> tableBinding,
    TextWriter logger)
{
    var query = from p in tableBinding select p;
    foreach (Person person in query)
    {
        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
            person.PartitionKey, person.RowKey, person.Name);
    }
}
```

### <a name="how-to-read-a-single-entity-from-a-table"></a>Jak odczytać pojedynczą jednostkę z tabeli

Istnieje konstruktor **atrybutów tabeli** z dwoma dodatkowymi parametrami, które umożliwiają określenie klucza partycji i klucza wiersza, gdy chcesz powiązać z jednostką pojedynczej tabeli.

Poniższy przykładowy kod odczytuje wiersz tabeli dla **jednostki Person** na podstawie wartości klucza partycji i klucza wiersza odebranych w komunikacie kolejki:

```csharp
public static void ReadTableEntity(
    [QueueTrigger("inputqueue")] Person personInQueue,
    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
    TextWriter logger)
{
    if (personInTable == null)
    {
        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                personInQueue.PartitionKey, personInQueue.RowKey);
    }
    else
    {
        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
    }
}
```

**Osoba** klasy w tym przykładzie nie ma do zaimplementowania **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Jak bezpośrednio używać interfejsu API magazynu .NET do pracy z tabelą

Można również użyć **Table** atrybut z **CloudTable** obiektu dla większej elastyczności w pracy z tabelą.

Poniższy przykładowy kod używa **CloudTable** obiektu, aby dodać jedną jednostkę do tabeli *transferu ruchu przychodzącego.*

```csharp
public static void UseStorageAPI(
    [Table("Ingress")] CloudTable tableBinding,
    TextWriter logger)
{
    var person = new Person()
        {
            PartitionKey = "Test",
            RowKey = "100",
            Name = "Name"
        };
    TableOperation insertOperation = TableOperation.Insert(person);
    tableBinding.Execute(insertOperation);
}
```

Aby uzyskać więcej informacji na temat korzystania z obiektu **CloudTable,** zobacz [Wprowadzenie do magazynu tabel platformy Azure przy użyciu platformy .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Tematy pokrewne objęte artykułu instrukcje dotyczące kolejek

Aby uzyskać informacje dotyczące obsługi przetwarzania tabel wyzwalanych przez komunikat kolejki lub scenariuszy zestawów SDK webjobs niespecyfizowanych dla przetwarzania tabel, zobacz Wprowadzenie do [usługi Azure Queue storage i usługi połączone z programem Visual Studio (projekty webjob).](../storage/vs-storage-webjobs-getting-started-queues.md)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze pracy z tabelami platformy Azure. Aby uzyskać więcej informacji na temat korzystania z usługi Azure WebJobs i webjobs SDK, zobacz [zasoby dokumentacji usługi Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).
