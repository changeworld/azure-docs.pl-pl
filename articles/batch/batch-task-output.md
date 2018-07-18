---
title: Utrwalanie wyników lub dzienników strumienia zakończonych zadań i zadań w magazynie danych — Azure Batch | Dokumentacja firmy Microsoft
description: Poznaj różne opcje utrwalanie danych wyjściowych z zadania wsadowe i zadania. Jednak można utrwalić dane do usługi Azure Storage lub do innego magazynu danych.
services: batch
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b578abfa6fc0a10edc5daab40f8a0eea5e6653d9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115066"
---
# <a name="persist-job-and-task-output"></a>Utrwalanie danych wyjściowych zadań i zadań podrzędnych

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Niektóre typowe dane wyjściowe zadania podrzędnego należą:

- Pliki tworzone podczas procesy zadań dane wejściowe.
- Pliki dziennika związane z wykonywaniem zadań. 

W tym artykule opisano różne opcje utrwalanie danych wyjściowych zadań podrzędnych oraz scenariusze, w których każda opcja jest najbardziej odpowiednie.   

## <a name="about-the-batch-file-conventions-standard"></a>O standardowej konwencji plików usługi Batch

Batch definiuje opcjonalny zestaw konwencje nazewnictwa plików wyjściowych zadania w usłudze Azure Storage. [Standardowe konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) zawiera opis tych konwencji. Standardowe konwencje plików określa nazwy kontenera i obiektów blob ścieżkę docelową w usłudze Azure Storage dla pliku wyjściowego danego na podstawie nazw zadanie i zadania.

To Ty możesz czy zdecydujesz się używać standardowy plik konwencje nazewnictwa plików danych wyjściowych danych. Można także nazwy kontenera docelowego i obiektów blob, ale chcesz. Jeśli należy używać standardowy plik konwencje nazewnictwa plików wyjściowych, a następnie swoje pliki wyjściowe są dostępne pod kątem wyświetlania w [witryny Azure portal][portal].

Istnieje kilka różnych sposobów, w których można używać standardowej konwencji plików:

- Korzystając z interfejsu API usługi Batch do utrwalania plików danych wyjściowych, istnieje możliwość Nazwa docelowego kontenerów i obiektów blob zgodnie ze standardem Konwencji plików. Interfejs API usługi Batch umożliwia utrwalanie plików danych wyjściowych z poziomu kodu klienta bez modyfikowania aplikacji zadań.
- Jeśli jest tworzona przy użyciu platformy .NET, możesz użyć [biblioteki Konwencji plików usługi Batch Azure dla platformy .NET][nuget_package]. Zaletą tej biblioteki jest Obsługa zapytań swoje pliki wyjściowe, zgodnie z jego identyfikator lub przeznaczenia. Wbudowane funkcje zapytań ułatwia dostęp do plików wyjściowych z aplikacji klienckiej lub od innych zadań. Jednak należy zmodyfikować aplikacji zadań w celu wywołania biblioteki Konwencji plików. Aby uzyskać więcej informacji, zobacz odwołania do [biblioteki Konwencji plików dla platformy .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Jeśli tworzysz z języka innego niż .NET, można zaimplementować standardowej konwencji plików w aplikacji.

## <a name="design-considerations-for-persisting-output"></a>Zagadnienia dotyczące projektowania dla utrwalanie danych wyjściowych 

Podczas projektowania rozwiązania usługi Batch, należy wziąć pod uwagę następujące czynniki związane z danych wyjściowych zadań i zadań.

* **Okres istnienia węzła obliczeniowego**: obliczenia węzły są często przejściowe, szczególnie w przypadku pul z włączoną funkcją automatycznego skalowania. Dane wyjściowe z zadania, który działa w węźle jest dostępna tylko wtedy, gdy istnieje węzeł, a tylko w okresie przechowywania plików ustawionych dla zadania. Jeśli zadanie generuje dane wyjściowe, które mogą być potrzebne po zakończeniu zadania, zadania należy przekazać jej pliki wyjściowe do trwałego magazynu, takich jak Azure Storage.

* **Dane wyjściowe magazynu**: usługi Azure Storage jest zalecane jako magazyn danych na potrzeby danych wyjściowych zadania, ale można użyć dowolnego z magazynu trwałego. Zapisywanie danych wyjściowych zadań podrzędnych do usługi Azure Storage jest zintegrowany interfejs API usługi Batch. Jeśli używasz innej formy trwałego magazynu, musisz pisanie logiki aplikacji, aby utrwalić samodzielnie wyjściowe zadania.   

* **Dane wyjściowe pobierania**: Jeśli utrwalonych danych wyjściowych zadania można pobrać dane wyjściowe zadania bezpośrednio z węzłów obliczeniowych w puli lub z usługi Azure Storage lub w innym magazynie danych. Aby pobrać dane wyjściowe zadania bezpośrednio w węźle obliczeniowym, należy nazwę pliku i jego lokalizacji danych wyjściowych w węźle. Jeśli będzie się powtarzać dane wyjściowe zadania do usługi Azure Storage, należy pełną ścieżkę do pliku w usłudze Azure Storage, aby pobrać pliki wyjściowe przy użyciu zestawu SDK usługi Azure Storage.

* **Wyświetlanie danych wyjściowych**: po przejściu do zadania usługi Batch w witrynie Azure portal i wybierz pozycję **pliki w węźle**, dostępne są wszystkie pliki skojarzone z zadaniem, nie tylko pliki wyjściowe interesuje Cię. Ponownie plików w węzłach obliczeniowych są dostępne tylko wtedy, gdy istnieje węzeł, a tylko w obrębie czas przechowywania pliku ustawionych dla zadania. Aby wyświetlić dane wyjściowe zadania, które zostały utrwalone w usłudze Azure Storage, można użyć witryny Azure portal lub aplikacji klienta usługi Azure Storage takich jak [Eksploratora usługi Azure Storage][storage_explorer]. Aby wyświetlić dane wyjściowe w usłudze Azure Storage za pomocą portalu lub inne narzędzie, należy znać lokalizację pliku i przejdź do niego bezpośrednio.

## <a name="options-for-persisting-output"></a>Opcje utrwalanie danych wyjściowych

Zależnie od scenariusza istnieje kilka różnych rozwiązań, które można wykonać, aby utrwalić dane wyjściowe zadania:

- Za pomocą interfejsu API usługi Batch.  
- Za pomocą biblioteki Konwencji plików usługi Batch dla platformy .NET.  
- Zaimplementuj standardowe konwencje plików usługi Batch w aplikacji.
- Implementowanie rozwiązania przenoszenia pliku niestandardowej.

W poniższych sekcjach opisano każde podejście bardziej szczegółowo.

### <a name="use-the-batch-service-api"></a>Za pomocą interfejsu API usługi Batch

W wersji 2017-05-01, usługa Batch dodaje obsługę określanie plików wyjściowych w usłudze Azure Storage dla danych zadań po użytkownik [Dodaj zadanie do zadania](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) lub [Dodaj to zbiór zadań podrzędnych do zadania](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Interfejs API usługi Batch obsługuje utrwalanie danych zadania do konta usługi Azure Storage z pul utworzonych za pomocą konfiguracji maszyny wirtualnej. Przy użyciu interfejsu API usługi Batch można utrwalić dane zadania bez modyfikowania aplikację, która działa zadanie. Opcjonalnie można stosować [standardowe konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) nazewnictwa plików, które pozostają do usługi Azure Storage. 

Użyj interfejsu API usługi Batch, aby utrwalić zadanie danych wyjściowych po:

- Chcesz zachować dane z zadania podrzędne usługi Batch i zadanie Menedżer zadania, w przypadku pul utworzonych za pomocą konfiguracji maszyny wirtualnej.
- Chcesz utrwalić dane w kontenerze usługi Azure Storage przy użyciu dowolnej nazwie.
- Aby utrwalić dane w kontenerze usługi Azure Storage, o nazwie zgodnie z opisem w [standardowe konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> Interfejs API usługi Batch nie obsługuje utrwalanie danych od zadania uruchomione w przypadku pul utworzonych za pomocą konfiguracji usługi w chmurze. Aby uzyskać informacji o zadaniu utrwalanie danych wyjściowych z pul uruchamianie konfiguracji usługi w chmurze, zobacz [utrwalanie danych i zadań do usługi Azure Storage za pomocą biblioteki Konwencji plików usługi Batch dla platformy .NET utrwalić ](batch-task-output-file-conventions.md)
> 
> 

Aby uzyskać więcej informacji na temat utrwalanie danych wyjściowych podzadań z interfejsem API usługi Batch, zobacz [utrwalanie zadań dane do usługi Azure Storage za pomocą usługi Batch usług interfejsu API](batch-task-output-files.md). Zobacz też [PersistOutputs] [ github_persistoutputs] przykładowy projekt w witrynie GitHub, który pokazuje, jak używać biblioteki klienta usługi Batch dla platformy .NET można utrwalić dane wyjściowe zadania do trwałego magazynu.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Korzystanie z biblioteki Konwencji plików usługi Batch dla platformy .NET

Tworzenie rozwiązań usługi Batch w języku C# i .NET deweloperzy mogą używać [biblioteki Konwencji plików dla platformy .NET] [ nuget_package] do utrwalenia danych zadania usługi Azure Storage account, zgodnie z do [pliku wsadowego Standardowe konwencje](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Biblioteki Konwencji plików obsługuje przenoszenie plików wyjściowych do usługi Azure Storage i nazewnictwa miejsce docelowe, kontenerów i obiektów blob w sposób, dobrze znane.

Biblioteki Konwencji plików obsługuje tworzenie zapytań pliki wyjściowe według Identyfikatora lub celu, co ułatwia ich znalezienia, bez konieczności całego pliku identyfikatorów URI. 

Biblioteki Konwencji plików usługi Batch dla platformy .NET umożliwia utrwalanie zadań, kiedy dane wyjściowe:

- Chcesz się przesyłanie strumieniowe danych do usługi Azure Storage, gdy zadanie jest uruchomione.
- Chcesz zachować dane z pul utworzonych za pomocą konfiguracji usługi w chmurze lub konfigurację maszyny wirtualnej.
- Aplikację kliencką lub innych zadań w ramach zadania musi zlokalizować i pobieranie plików wyjściowych zadania według Identyfikatora lub według celu. 
- Chcesz wykonać kontrolnych lub wczesnej przekazywania początkowe wyniki.
- Chcesz wyświetlić dane wyjściowe zadania w witrynie Azure portal.

Aby uzyskać więcej informacji na temat utrwalanie danych wyjściowych zadania za pomocą biblioteki Konwencji plików dla platformy .NET, zobacz [utrwalanie danych i zadań do usługi Azure Storage za pomocą biblioteki Konwencji plików usługi Batch dla platformy .NET w celu utrwalenia ](batch-task-output-file-conventions.md). Zobacz też [PersistOutputs] [ github_persistoutputs] przykładowy projekt w witrynie GitHub, który pokazuje, jak zachować dane wyjściowe zadania do trwałego magazynu za pomocą biblioteki Konwencji plików dla platformy .NET.

[PersistOutputs] [ github_persistoutputs] przykładowy projekt w witrynie GitHub pokazuje sposób użycia biblioteki klienta usługi Batch dla platformy .NET, aby utrwalić dane wyjściowe zadania do trwałego magazynu.

### <a name="implement-the-batch-file-conventions-standard"></a>Zaimplementuj standardowe konwencje plików usługi Batch

Jeśli używasz języka innego niż .NET można zaimplementować [standardowe konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) we własnej aplikacji. 

Można zaimplementować zasady nazewnictwa konwencje plików samodzielnie zostanie sprawdzone schemat nazewnictwa, lub jeśli chcesz wyświetlić dane wyjściowe zadania w witrynie Azure portal.

### <a name="implement-a-custom-file-movement-solution"></a>Implementowanie rozwiązania przenoszenia niestandardowego pliku

Można także zaimplementować własnego rozwiązania przeniesienia całego pliku. Użyj tego podejścia, gdy:

- Chcesz zachować dane zadania do magazynu danych innego niż usługi Azure Storage. Aby przekazać pliki do magazynu danych, takich jak Azure SQL czy Azure DataLake, należy utworzyć własny skrypt lub plik wykonywalny do przekazania do tej lokalizacji. Następnie możesz wywołać go w wierszu polecenia po uruchomieniu podstawowy plik wykonywalny. Na przykład w węźle Windows, może wywołać te dwa polecenia: `doMyWork.exe && uploadMyFilesToSql.exe`
- Chcesz wykonać kontrolnych lub wczesnej przekazywania początkowe wyniki.
- Chcesz zachować kontrolę nad obsługi błędów. Na przykład można implementować własne rozwiązanie, jeśli chcesz użyć akcji zależności zadań do wykonywania pewnych akcji przekazywania na podstawie kodów zakończenia określonego zadania. Aby uzyskać więcej informacji na temat akcji zależności zadań, zobacz [tworzenia zależności zadań podrzędnych do uruchomienia zadania, które są zależne od innych zadań](batch-task-dependencies.md). 

## <a name="next-steps"></a>Kolejne kroki

- Zapoznaj się korzystać z nowych funkcji interfejsu API usługi Batch, aby zachować dane zadania w [utrwalanie zadań dane do usługi Azure Storage za pomocą usługi Batch usług interfejsu API](batch-task-output-files.md).
- Dowiedz się więcej o za pomocą biblioteki Konwencji plików usługi Batch dla platformy .NET w [utrwalanie danych i zadań do usługi Azure Storage za pomocą biblioteki Konwencji plików usługi Batch dla platformy .NET](batch-task-output-file-conventions.md).
- Zobacz [PersistOutputs] [ github_persistoutputs] przykładowy projekt w witrynie GitHub, który pokazuje, jak korzystać zarówno z biblioteki klienta usługi Batch dla platformy .NET i biblioteki Konwencji plików dla platformy .NET można utrwalić dane wyjściowe zadania do trwałego magazynu .

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
