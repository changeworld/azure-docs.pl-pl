---
title: Wprowadzenie do magazynu Azure i programu Visual Studio połączone usługi (zadania WebJob projekty)
description: Jak rozpocząć pracę przy użyciu magazynu tabel Azure w projekcie zadań Webjob Azure w programie Visual Studio po połączeniu z kontem magazynu za pomocą programu Visual Studio połączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 78ef7d98d9e11ba9b2c52bfc88d6ecb050cafa84
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793304"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Wprowadzenie do korzystania z usługi Azure magazynu (projekty zadanie WebJob platformy Azure)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Przegląd
Ten artykuł zawiera C# przykłady pokazujące pokazują, jak korzystać z wersji zestawu Azure WebJobs SDK 1.x z usługi Magazyn tabel Azure. Kod przykłady użycia [zestaw SDK zadań Webjob](https://github.com/Azure/azure-webjobs-sdk/wiki) wersja 1.x.

Usługa Azure Table storage umożliwia przechowywania dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z wewnątrz lub na zewnątrz w chmurze Azure. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.  Zobacz [Rozpoczynanie pracy z magazynem tabel Azure przy użyciu platformy .NET](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table) Aby uzyskać więcej informacji.

Niektóre Pokaż wstawki kodu **tabeli** atrybutu używanego w funkcje, które są nazywane ręcznie, oznacza to, nie za pomocą jednego z atrybutów wyzwalacza.

## <a name="how-to-add-entities-to-a-table"></a>Jak dodać jednostek do tabeli
Aby dodać jednostek do tabeli, należy użyć **tabeli** atrybutem **ICollector<T>**  lub **IAsyncCollector<T>**  parametru gdzie **T** Określa schemat jednostek, które chcesz dodać. Konstruktor atrybutu ma parametr typu string, który określa nazwę tabeli.

Poniższy przykładowy kod dodaje **osoby** jednostek do tabeli o nazwie *wejściowych*.

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

Zwykle typ używających **ICollector** pochodną **TableEntity** lub implementuje **ITableEntity**, ale nie ma. Jedną z następujących **osoby** klasy pracy kodem przedstawionym w poprzednim **wejściowych** — metoda.

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

Jeśli chcesz pracować bezpośrednio za pomocą interfejsu API magazynu Azure, można dodać **CloudStorageAccount** parametru w podpisie metody.

## <a name="real-time-monitoring"></a>Monitorowanie w czasie rzeczywistym
Ponieważ danych wejściowych funkcji często przetwarzania dużych ilości danych, zestaw SDK zadań Webjob pulpitu nawigacyjnego zawiera dane monitorowania w czasie rzeczywistym. **Dziennika wywołania** sekcji informuje, czy funkcja jest nadal uruchomiony.

![Transfer danych przychodzących systemem — funkcja](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

**Szczegóły wywołania** raporty strony postępu funkcji (liczba jednostek zapisywane) jest uruchomiona, i daje możliwość jego przerwanie.

![Transfer danych przychodzących systemem — funkcja](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Po zakończeniu działania funkcji **szczegóły wywołania** liczbę wierszy, zapisany raporty strony.

![Transfer danych przychodzących Zakończono — funkcja](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Jak można odczytać wiele jednostek z tabeli
Aby odczytać tabeli, należy użyć **tabeli** atrybutem **IQueryable<T>**  parametru wpisuje **T** pochodną **TableEntity** lub implementuje **ITableEntity**.

Poniższy przykładowy kod odczytuje i rejestruje wszystkie wiersze z **wejściowych** tabeli:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Jak można odczytać pojedyncza jednostka z tabeli
Brak **tabeli** konstruktora atrybutu z dwóch dodatkowych parametrów, które pozwalają określić klucz partycji i klucz wiersza, jeśli chcesz powiązać jednostki pojedynczej tabeli.

Poniższy przykładowy kod odczytuje wiersz tabeli **osoby** jednostki na podstawie partycji klucza i wiersz klucza wartości otrzymane w wiadomości w kolejce:  

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


**Osoby** klasy w tym przykładzie nie musi implementować **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Sposób użycia interfejsu API programu .NET magazynu bezpośrednio do pracy z tabeli
Można również użyć **tabeli** atrybutem **CloudTable** obiektu dla większą elastyczność podczas pracy z tabelą.

Poniższy kod przykładowy używa **CloudTable** obiekt do dodania do pojedynczej jednostki *wejściowych* tabeli.

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

Aby uzyskać więcej informacji o sposobie używania **CloudTable** obiektów, zobacz [Rozpoczynanie pracy z magazynem tabel Azure przy użyciu platformy .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Tematy pokrewne objętych artykule kolejek
Informacje o sposobie obsługi przetwarzania tabeli wyzwalane przez komunikatu w kolejce, lub zestaw SDK zadań Webjob scenariusze nie są typowe dla przetwarzania tabeli, zobacz [Rozpoczynanie pracy z magazynem kolejek Azure i programu Visual Studio połączone usługi (projekty zadania WebJob)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule udostępnił przykłady kodu, które przedstawiają sposób obsługi typowe scenariusze dotyczące pracy z tabel Azure. Aby uzyskać więcej informacji o sposobie używania zadań Webjob Azure i zestaw SDK zadań Webjob, zobacz [zasoby dokumentacji zadań Webjob Azure](http://go.microsoft.com/fwlink/?linkid=390226).

