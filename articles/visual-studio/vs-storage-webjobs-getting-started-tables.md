---
title: Wprowadzenie do usługi Azure storage i Visual Studio podłączone usługi (projekty, zadania WebJob)
description: Jak rozpocząć pracę, przy użyciu usługi Azure Table storage w projekcie usługi Azure WebJobs w programie Visual Studio po nawiązaniu połączenia z kontem magazynu za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: a9a4475465fefb01ec53e6e0eb814f9b8f192a1b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799332"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Wprowadzenie do usługi Azure Storage (projekty, zadanie WebJob platformy Azure)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Omówienie
Ten artykuł zawiera C# przykłady kodu, które pokazują, jak używać zestawu Azure WebJobs SDK w wersji 1.x za pomocą usługi Azure table storage. Przykłady kodu, użyj [zestawu SDK usługi WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) wersji 1.x.

Usługa Azure Table storage umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z wewnątrz i na zewnątrz chmury platformy Azure. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.  Zobacz [Rozpoczynanie pracy z usługą Azure Table storage przy użyciu platformy .NET](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) Aby uzyskać więcej informacji.

Niektóre show fragmenty kodu **tabeli** atrybut używany w funkcjach, które są wywoływane ręcznie, oznacza to, że nie przy użyciu jednego z atrybutów wyzwalacza.

## <a name="how-to-add-entities-to-a-table"></a>Jak dodać jednostki do tabeli
Aby dodać jednostki do tabeli, użyj **tabeli** atrybutem **ICollector<T>**  lub **IAsyncCollector<T>**  parametru gdzie **T** Określa schemat jednostek, które chcesz dodać. Konstruktor atrybutu ma parametr ciągu, który określa nazwę tabeli.

Poniższy przykładowy kod dodaje **osoby** jednostki do tabeli o nazwie *ruch przychodzący*.

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

Zazwyczaj typu można użyć z **ICollector** pochodzi od klasy **TableEntity** lub implementuje **ITableEntity**, ale nie musi. Jedno z następujących **osoby** działania klas z kodu pokazanego w poprzednim **ruch przychodzący** metody.

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

Jeśli chcesz współpracować bezpośrednio z interfejsu API usługi Azure storage, można dodać **CloudStorageAccount** parametr podpis metody.

## <a name="real-time-monitoring"></a>Monitorowanie w czasie rzeczywistym
Ponieważ funkcje transferu danych przychodzących danych często przetwarzają duże ilości danych, zestaw SDK usługi WebJobs pulpitu nawigacyjnego zawiera dane monitorowania w czasie rzeczywistym. **Dziennik wywołań** sekcji informuje, czy funkcja jest nadal uruchomiona.

![Ruch przychodzący funkcji działającej](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

**Szczegóły wywołania** strona Raporty postępu funkcji (liczba jednostek zapisane) jest uruchomiona, i daje możliwość jej przerwać.

![Ruch przychodzący funkcji działającej](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Po zakończeniu funkcji **szczegóły wywołania** strona Raporty liczba zapisanych wierszy.

![Zakończono funkcja transferu danych przychodzących](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Jak odczytać wiele jednostek z tabeli
Do odczytywania tabeli, użyj **tabeli** atrybutem **IQueryable<T>**  parametru wpisuje **T** pochodzi od klasy **TableEntity**lub implementuje **ITableEntity**.

Poniższy przykładowy kod odczytuje i rejestruje wszystkie wiersze z **ruch przychodzący** tabeli:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Jak odczytać pojedynczej jednostki z tabeli
Brak **tabeli** konstruktora atrybutu z dwóch dodatkowych parametrów, które umożliwiają określenie klucza partycji i klucz wiersza, jeśli chcesz powiązać z jednostką pojedynczej tabeli.

Poniższy przykładowy kod odczytuje wiersz tabeli **osoby** jednostki oparte na klucz i wiersz wartości kluczy partycji trafiła do komunikatu w kolejce:  

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


**Osoby** klasy, w tym przykładzie nie trzeba implementować **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Jak używać interfejsu API programu .NET Storage bezpośrednio do pracy z tabelą
Można również użyć **tabeli** atrybutem **CloudTable** obiekt zapewnia większą elastyczność w stosowaniu tabeli.

Poniższy kod przykładowy używa **CloudTable** obiekt do dodania do pojedynczej jednostki *ruch przychodzący* tabeli.

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

Aby uzyskać więcej informacji o sposobie używania **CloudTable** obiektu, zobacz [Rozpoczynanie pracy z usługą Azure Table storage przy użyciu platformy .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Tematy pokrewne objętych artykule kolejek
Aby uzyskać informacje dotyczące obsługi przetwarzania tabeli wyzwolone przez komunikatu w kolejce lub dla zestawu SDK usługi WebJobs scenariuszy nie odnoszą się do przetwarzania tabeli, zobacz [wprowadzenie do usługi Azure Queue storage i Visual Studio podłączone usługi (projekty, zadania WebJob) ](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule udostępnił przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze dotyczące pracy z tabelami platformy Azure. Aby uzyskać więcej informacji o sposobie używania usługi Azure WebJobs i zestaw SDK zadań Webjob, zobacz [zasoby dokumentacji usługi Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

