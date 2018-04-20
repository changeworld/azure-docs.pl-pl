---
title: Utrwalanie wyników lub dzienniki z zakończonych zadań i zadań w magazynie danych - partii zadań Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o różnych opcjach trwałych danych wyjściowych z zadań i zadań wsadowych. Można ją utrwalić danych do magazynu Azure lub do innego magazynu danych.
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
ms.openlocfilehash: cb8b1ca3514e27221e95cb2def823c8f89d151e5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="persist-job-and-task-output"></a>Utrwalanie danych wyjściowych zadań i zadań podrzędnych

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Typowe przykłady dane wyjściowe zadania obejmują:

- Pliki utworzone podczas procesów zadań dane wejściowe.
- Pliki dziennika związane z wykonywania zadania. 

W tym artykule opisano różne opcje trwałych danych wyjściowych zadania i scenariusze, dla których jest najbardziej odpowiednia każdej opcji.   

## <a name="about-the-batch-file-conventions-standard"></a>O standardowej konwencji pliku wsadowego

Wsadowe definiuje opcjonalny zestaw konwencji nazewnictwa plików dane wyjściowe zadania w usłudze Azure Storage. [Standard konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) zawiera opis tych konwencji. Standard konwencje plików określa nazwy kontenera i obiektów blob ścieżki docelowej w usłudze Azure Storage dla pliku wyjściowego danego na podstawie nazw zadań i zadań.

Jest możesz czy zdecydujesz się użyć standardowego pliku konwencje nazewnictwa plików danych wyjściowych. Można również nazwę kontenera docelowego i obiektów blob, jednak chcesz. Jeśli używasz standardowego pliku konwencje nazewnictwa plików wyjściowych, a następnie Twoje pliki wyjściowe są dostępne do wyświetlenia w [portalu Azure][portal].

Istnieje kilka sposobów użyć standardowego pliku konwencje:

- Aby zachować pliki wyjściowe korzystasz z interfejsu API usługi partii, istnieje możliwość Nazwa docelowego kontenerów i obiektów blob według standardu konwencje plików. Usługi interfejsu API partii umożliwia utrwalić pliki wyjściowe z kodu klienta bez modyfikowania aplikacji zadań.
- Jeśli tworzysz .NET, możesz użyć [biblioteki konwencje plików usługi partia zadań Azure dla platformy .NET][nuget_package]. Zaletą korzystania z tej biblioteki jest, że obsługuje badania plików wyjściowych zgodnie z ich identyfikator i celu. Wbudowanej funkcji podczas badania ułatwia dostęp do danych wyjściowych plików z aplikacji klienckiej lub innych zadań. Jednak aplikacja zadań muszą zostać zmodyfikowane do wywoływania biblioteki konwencje plików. Aby uzyskać więcej informacji, zobacz dokumentację dla [konwencje plików biblioteki dla platformy .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Jeśli tworzysz języka innego niż .NET, można zaimplementować standardowej konwencji plik w aplikacji.

## <a name="design-considerations-for-persisting-output"></a>Zagadnienia dotyczące projektowania dla trwałych danych wyjściowych 

Podczas projektowania rozwiązania partii, należy wziąć pod uwagę następujące czynniki związane z danych wyjściowych i zadań.

* **Okres istnienia węzła obliczeniowe**: obliczeniowe są często przejściowe, szczególnie w pule włączona funkcja automatycznego skalowania. Dane wyjściowe zadania w węźle jest dostępna tylko wtedy, gdy węzeł istnieje, i tylko w okresie przechowywania pliku ustawiono dla zadania. Jeśli zadanie generuje dane wyjściowe, które mogą być wymagane po ukończeniu zadania, zadania należy przesłać jego pliki wyjściowe do trwałego magazynu, takie jak magazyn Azure.

* **Dane wyjściowe magazynu**: Magazyn Azure jest zalecane jako dane wyjściowe zadania w magazynie danych, ale można użyć dowolnego magazynu trwałego. Zapisywanie danych wyjściowych zadania do usługi Azure Storage jest zintegrowany z interfejsu API usługi partii. Jeśli używasz innej formy trwałego magazynu, musisz pisanie logiki aplikacji, aby utrwalić produkty wyjściowe samodzielnie zadania.   

* **Dane wyjściowe pobierania**: Jeśli utrwalonych danych wyjściowych zadania można pobrać dane wyjściowe zadania bezpośrednio z węzłów obliczeniowych w puli lub usługi Azure Storage lub innym magazynie danych. Aby pobrać dane wyjściowe zadania bezpośrednio w węźle obliczeń, należy nazwę pliku i jego lokalizacji danych wyjściowych w węźle. Jeśli będzie się powtarzać dane wyjściowe zadania do magazynu Azure, należy pełną ścieżkę do pliku w magazynie Azure, aby pobrać pliki wyjściowe z zestawu SDK usługi Magazyn Azure.

* **Wyświetlanie danych wyjściowych**: po przejściu do zadania wsadowego na platformie Azure, portalu i wybierz pozycję **pliki w węźle**, wyświetlane są wszystkie pliki skojarzone z zadaniem, nie tylko pliki wyjściowe Cię interesują. Ponownie plików w węzłach obliczeniowych są dostępne tylko wtedy, gdy istnieje węzeł i tylko w obrębie czas przechowywania pliku ustawiono dla zadania. Aby wyświetlić dane wyjściowe zadania, które zostały utrwalone w magazynie Azure, można użyć portalu Azure lub aplikacji klienta usługi Azure Storage takich jak [Eksploratora usługi Storage Azure][storage_explorer]. Do wyświetlenia danych wyjściowych w usłudze Azure Storage za pomocą portalu lub innego narzędzia, należy znać lokalizacji pliku i przejść bezpośrednio do niego.

## <a name="options-for-persisting-output"></a>Opcje dla trwałych danych wyjściowych

W zależności od scenariusza istnieje kilka różne podejścia, które należy wykonać, aby zachować dane wyjściowe zadania:

- Za pomocą usługi interfejsu API partii.  
- Korzystanie z biblioteki konwencje pliku wsadowego dla platformy .NET.  
- Wdrożenie standardowe konwencje pliku wsadowego w aplikacji.
- Implementacji niestandardowego pliku rozwiązania przepływu.

W poniższych sekcjach opisano każdego z podejść bardziej szczegółowo.

### <a name="use-the-batch-service-api"></a>Za pomocą interfejsu API usługi partii

Z wersją 2017-05-01, usługa partia zadań dodaje obsługę określanie plików wyjściowych w usłudze Azure Storage dla danych zadań podczas możesz [dodawania zadania do zadania](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) lub [dodać kolekcję zadań do zadania](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Usługi interfejsu API partii obsługuje trwałych danych zadania konta usługi Azure Storage z zestawów utworzonych za pomocą konfiguracji maszyny wirtualnej. Przy użyciu interfejsu API usługi partii można utrwalić danych zadania bez modyfikowania aplikacji, która uruchamia zadania. Można opcjonalnie stosować się do [standard konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) nazewnictwa plików, które pozostają do magazynu Azure. 

Użyj interfejsu API usługi partii do utrzymania produkty wyjściowe kiedy zadania:

- Chcesz zachować dane z zadania wsadowego i zadania Menedżer zadania w pulach utworzone za pomocą konfiguracji maszyny wirtualnej.
- Chcesz zachować dane do kontenera magazynu Azure za pomocą dowolnego nazwy.
- Aby zachować dane do kontenera usługi Magazyn Azure o nazwie zgodnie z [standard konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> Usługi interfejsu API partii nie obsługuje trwałych danych z zadań uruchomionych w pulach utworzone z konfiguracją usługi w chmurze. Aby uzyskać informacji o zadaniu utrwalanie dane wyjściowe z pul systemem konfiguracji usługi w chmurze, zobacz [utrwalenia danych i zadań do magazynu Azure z biblioteką konwencje pliku wsadowego dla platformy .NET utrwalić ](batch-task-output-file-conventions.md)
> 
> 

Aby uzyskać więcej informacji na trwałych danych wyjściowych zadania przy użyciu interfejsu API usługi partii, zobacz [utrwalanie zadań dane do magazynu Azure z instancją usług interfejsu API](batch-task-output-files.md). Zobacz też [PersistOutputs] [github_persistoutputs] przykładowy projekt w witrynie GitHub, który pokazuje, jak używać biblioteki klienta usługi partia zadań dla platformy .NET do utrwalenia danych wyjściowych zadania do magazynu trwałego.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Korzystanie z biblioteki konwencje pliku wsadowego dla platformy .NET

Tworzenie rozwiązań partii i C# .NET deweloperzy mogą używać [konwencje plików biblioteki dla platformy .NET] [ nuget_package] do utrwalenia danych zadań do magazynu Azure konta, zgodnie z do [pliku wsadowego Konwencje standardowych](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Biblioteka konwencje plików obsługuje przenoszenie plików wyjściowych do usługi Azure Storage i nazewnictwa docelowego kontenerów i obiektów blob w sposób dobrze znany.

Biblioteka konwencje plików obsługuje podczas badania plików wyjściowych według Identyfikatora lub cel, co ułatwia ich znalezienia, bez konieczności pliku pełne identyfikatory URI. 

Użyj biblioteki konwencje pliku wsadowego dla platformy .NET do utrzymania produkty wyjściowe kiedy zadania:

- Chcesz strumienia danych do magazynu Azure, podczas gdy zadanie jest uruchomione.
- Chcesz zachować dane z zestawów utworzonych za pomocą konfiguracji usługi w chmurze lub konfiguracji maszyny wirtualnej.
- Aplikacja kliencka lub innych zadań w zadaniu musi znaleźć i pobrać pliki wyjściowe zadań według Identyfikatora lub cel. 
- Chcesz przekazywanie wskazanie wyboru lub wczesne początkowej wyników.
- Chcesz wyświetlić dane wyjściowe zadania w portalu Azure.

Aby uzyskać więcej informacji na trwałych danych wyjściowych zadania z biblioteką konwencje plików dla platformy .NET, zobacz [utrwalenia danych i zadań do magazynu Azure z biblioteką konwencje pliku wsadowego dla platformy .NET utrwalić ](batch-task-output-file-conventions.md). Zobacz też [PersistOutputs] [github_persistoutputs] przykładowy projekt w witrynie GitHub, który pokazuje, jak używać biblioteki konwencje plików dla platformy .NET do utrwalenia danych wyjściowych zadania do magazynu trwałego.

[PersistOutputs] [github_persistoutputs] przykładowy projekt w witrynie GitHub pokazano, jak używać biblioteki klienta usługi partia zadań dla platformy .NET do utrwalenia danych wyjściowych zadania do magazynu trwałego.

### <a name="implement-the-batch-file-conventions-standard"></a>Wdrożenie standardowe konwencje pliku wsadowego

Jeśli używasz języka innego niż .NET można zaimplementować [standard konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) w swojej aplikacji. 

Można zaimplementować pliku konwencje nazewnictwa samodzielnie sprawdzonych schemat nazewnictwa, lub jeśli chcesz wyświetlić dane wyjściowe zadania w portalu Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementacji niestandardowego pliku rozwiązania przepływu

Można też wdrożyć własne rozwiązania przeniesienia całego pliku. Użyj tego podejścia, gdy:

- Chcesz zachować dane zadania w magazynie danych innych niż Azure Storage. Aby przekazać pliki do magazynu danych, takich jak bazy danych Azure SQL lub danych Azure, należy utworzyć niestandardowego skryptu lub pliku wykonywalnego do przekazania do tej lokalizacji. Można następnie wywołać ją w wierszu polecenia po uruchomieniu podstawowego pliku wykonywalnego. Na przykład w węźle systemu Windows, należy wywołać następujące dwa polecenia: `doMyWork.exe && uploadMyFilesToSql.exe`
- Chcesz przekazywanie wskazanie wyboru lub wczesne początkowej wyników.
- Chcesz zachować kontrolę nad obsługi błędów. Na przykład można zaimplementować własne rozwiązanie, jeśli chcesz użyć akcje zależności zadań do wykonania niektórych akcji przekazywania na podstawie kodów zakończenia określonego zadania. Aby uzyskać więcej informacji na akcje zależności zadań, zobacz [utworzyć zależności zadań do wykonywania zadań, które są zależne od innych zadań](batch-task-dependencies.md). 

## <a name="next-steps"></a>Kolejne kroki

- Eksplorowania przy użyciu nowych funkcji w interfejsie API partii usługi do utrwalenia danych zadania w [utrwalanie zadań dane do magazynu Azure z instancją usług interfejsu API](batch-task-output-files.md).
- Więcej informacji na temat korzystania z biblioteki konwencje pliku wsadowego dla platformy .NET w [utrwalenia danych i zadań do magazynu Azure z biblioteką konwencje pliku wsadowego dla platformy .NET utrwalić ](batch-task-output-file-conventions.md).
- Zobacz [PersistOutputs] [github_persistoutputs] przykładowy projekt w witrynie GitHub, w którym przedstawiono sposób użycia obu partii bibliotece klienta usługi .NET i biblioteki konwencje plików dla platformy .NET do utrwalenia danych wyjściowych zadania do magazynu trwałego.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
